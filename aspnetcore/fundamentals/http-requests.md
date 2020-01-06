---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
uid: fundamentals/http-requests
ms.openlocfilehash: f2494a5815396e693f6fd2a45ad78ebffe4d54a3
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358076"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="d2bc3-103">Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2bc3-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d2bc3-104">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d2bc3-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="d2bc3-105">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="d2bc3-106">o `IHttpClientFactory` oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="d2bc3-107">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-108">Por exemplo, um cliente chamado *GitHub* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="d2bc3-109">Um cliente padrão pode ser registrado para acesso geral.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="d2bc3-110">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="d2bc3-111">Fornece extensões para o middleware baseado em Polly para tirar proveito da delegação de manipuladores no `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="d2bc3-112">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="d2bc3-113">O gerenciamento automático evita problemas comuns de DNS (sistema de nomes de domínio) que ocorrem ao gerenciar manualmente `HttpClient` tempos de vida.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d2bc3-114">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d2bc3-115">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d2bc3-116">O código de exemplo nesta versão de tópico usa <xref:System.Text.Json> para desserializar o conteúdo JSON retornado em respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="d2bc3-117">Para obter exemplos que usam `Json.NET` e `ReadAsAsync<T>`, use o seletor de versão para selecionar uma versão 2. x deste tópico.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="d2bc3-118">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-118">Consumption patterns</span></span>

<span data-ttu-id="d2bc3-119">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="d2bc3-120">Uso básico</span><span class="sxs-lookup"><span data-stu-id="d2bc3-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="d2bc3-121">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="d2bc3-122">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="d2bc3-123">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="d2bc3-124">A melhor abordagem depende dos requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="d2bc3-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="d2bc3-125">Basic usage</span></span>

<span data-ttu-id="d2bc3-126">`IHttpClientFactory` pode ser registrado chamando `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="d2bc3-127">Um `IHttpClientFactory` pode ser solicitado usando [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d2bc3-128">O código a seguir usa `IHttpClientFactory` para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="d2bc3-129">Usar `IHttpClientFactory` como no exemplo anterior é uma boa maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="d2bc3-130">Ele não tem impacto sobre como `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="d2bc3-131">Em locais onde `HttpClient` instâncias são criadas em um aplicativo existente, substitua essas ocorrências por chamadas para <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="d2bc3-132">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-132">Named clients</span></span>

<span data-ttu-id="d2bc3-133">Os clientes nomeados são uma boa opção quando:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="d2bc3-134">O aplicativo requer muitos usos distintos de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="d2bc3-135">Muitos `HttpClient`s têm configuração diferente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="d2bc3-136">A configuração para um `HttpClient` nomeado pode ser especificada durante o registro no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="d2bc3-137">No código anterior, o cliente está configurado com:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="d2bc3-138">O endereço base `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="d2bc3-139">Dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="d2bc3-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="d2bc3-140">CreateClient</span></span>

<span data-ttu-id="d2bc3-141">Cada vez que <xref:System.Net.Http.IHttpClientFactory.CreateClient*> é chamado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="d2bc3-142">Uma nova instância do `HttpClient` é criada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="d2bc3-143">A ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-143">The configuration action is called.</span></span>

<span data-ttu-id="d2bc3-144">Para criar um cliente nomeado, passe seu nome para `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="d2bc3-145">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="d2bc3-146">O código pode passar apenas o caminho, já que o endereço base configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="d2bc3-147">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-147">Typed clients</span></span>

<span data-ttu-id="d2bc3-148">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-148">Typed clients:</span></span>

* <span data-ttu-id="d2bc3-149">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="d2bc3-150">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="d2bc3-151">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="d2bc3-152">Por exemplo, um único cliente tipado pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="d2bc3-153">Para um ponto de extremidade de back-end único.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="d2bc3-154">Para encapsular toda a lógica lidando com o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="d2bc3-155">Trabalhe com DI e pode ser injetado onde necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="d2bc3-156">Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="d2bc3-157">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-157">In the preceding code:</span></span>

* <span data-ttu-id="d2bc3-158">A configuração é movida para o cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="d2bc3-159">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="d2bc3-160">Métodos específicos de API podem ser criados para expor `HttpClient` funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="d2bc3-161">Por exemplo, o método `GetAspNetDocsIssues` encapsula o código para recuperar problemas abertos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="d2bc3-162">O código a seguir chama <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> em `Startup.ConfigureServices` para registrar uma classe de cliente tipada:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="d2bc3-163">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="d2bc3-164">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="d2bc3-165">A configuração de um cliente tipado pode ser especificada durante o registro no `Startup.ConfigureServices`, em vez de no construtor do cliente digitado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="d2bc3-166">O `HttpClient` pode ser encapsulado em um cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="d2bc3-167">Em vez de expô-lo como uma propriedade, defina um método que chame a instância de `HttpClient` internamente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="d2bc3-168">No código anterior, a `HttpClient` é armazenada em um campo particular.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="d2bc3-169">O acesso ao `HttpClient` é pelo método `GetRepos` público.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="d2bc3-170">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-170">Generated clients</span></span>

<span data-ttu-id="d2bc3-171">`IHttpClientFactory` pode ser usado em combinação com bibliotecas de terceiros, como [rEFIt](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="d2bc3-172">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="d2bc3-173">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="d2bc3-174">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="d2bc3-175">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="d2bc3-176">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-176">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="d2bc3-177">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="d2bc3-178">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="d2bc3-178">Outgoing request middleware</span></span>

<span data-ttu-id="d2bc3-179">`HttpClient` tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="d2bc3-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="d2bc3-181">Simplifica a definição dos manipuladores a serem aplicados para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="d2bc3-182">Dá suporte ao registro e encadeamento de vários manipuladores para criar um pipeline de middleware de solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d2bc3-183">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="d2bc3-184">Esse padrão:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-184">This pattern:</span></span>

  * <span data-ttu-id="d2bc3-185">É semelhante ao pipeline de middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="d2bc3-186">Fornece um mecanismo para gerenciar preocupações abrangentes em relação a solicitações HTTP, como:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="d2bc3-187">caching</span><span class="sxs-lookup"><span data-stu-id="d2bc3-187">caching</span></span>
    * <span data-ttu-id="d2bc3-188">tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="d2bc3-188">error handling</span></span>
    * <span data-ttu-id="d2bc3-189">serialização</span><span class="sxs-lookup"><span data-stu-id="d2bc3-189">serialization</span></span>
    * <span data-ttu-id="d2bc3-190">log</span><span class="sxs-lookup"><span data-stu-id="d2bc3-190">logging</span></span>

<span data-ttu-id="d2bc3-191">Para criar um manipulador de delegação:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-191">To create a delegating handler:</span></span>

* <span data-ttu-id="d2bc3-192">Derive de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="d2bc3-193">Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="d2bc3-194">Execute o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="d2bc3-195">O código anterior verifica se o cabeçalho de `X-API-KEY` está na solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="d2bc3-196">Se `X-API-KEY` estiver ausente, <xref:System.Net.HttpStatusCode.BadRequest> será retornado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="d2bc3-197">Mais de um manipulador pode ser adicionado à configuração para um `HttpClient` com <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-197">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d2bc3-198">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="d2bc3-199">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="d2bc3-200">Os manipuladores podem depender dos serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="d2bc3-201">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="d2bc3-202">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="d2bc3-203">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="d2bc3-204">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="d2bc3-205">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="d2bc3-206">Passe dados para o manipulador usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="d2bc3-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="d2bc3-208">Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="d2bc3-209">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-209">Use Polly-based handlers</span></span>

<span data-ttu-id="d2bc3-210">o `IHttpClientFactory` integra-se com a biblioteca de terceiros [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="d2bc3-211">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="d2bc3-212">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="d2bc3-213">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-214">As extensões Polly dão suporte à adição de manipuladores baseados em Polly a clientes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="d2bc3-215">Polly requer o pacote NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="d2bc3-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="d2bc3-216">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="d2bc3-216">Handle transient faults</span></span>

<span data-ttu-id="d2bc3-217">As falhas normalmente ocorrem quando chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="d2bc3-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permite que uma política seja definida para lidar com erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="d2bc3-219">As políticas configuradas com `AddTransientHttpErrorPolicy` manipulam as seguintes respostas:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="d2bc3-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="d2bc3-220">HTTP 5xx</span></span>
* <span data-ttu-id="d2bc3-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="d2bc3-221">HTTP 408</span></span>

<span data-ttu-id="d2bc3-222">`AddTransientHttpErrorPolicy` fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="d2bc3-223">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="d2bc3-224">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="d2bc3-225">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="d2bc3-225">Dynamically select policies</span></span>

<span data-ttu-id="d2bc3-226">Os métodos de extensão são fornecidos para adicionar manipuladores baseados em Polly, por exemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="d2bc3-227">A sobrecarga de `AddPolicyHandler` a seguir inspeciona a solicitação para decidir qual política aplicar:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="d2bc3-228">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="d2bc3-229">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="d2bc3-230">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="d2bc3-231">É comum aninhar políticas Polly:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="d2bc3-232">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-232">In the preceding example:</span></span>

* <span data-ttu-id="d2bc3-233">Dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-233">Two handlers are added.</span></span>
* <span data-ttu-id="d2bc3-234">O primeiro manipulador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="d2bc3-235">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="d2bc3-236">A segunda chamada de `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="d2bc3-237">Outras solicitações externas serão bloqueadas por 30 segundos se 5 tentativas com falha ocorrerem em sequência.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="d2bc3-238">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="d2bc3-239">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="d2bc3-240">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="d2bc3-241">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="d2bc3-242">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-242">In the following code:</span></span>

* <span data-ttu-id="d2bc3-243">As políticas "regular" e "longa" são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="d2bc3-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adiciona as políticas "regular" e "longa" do registro.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="d2bc3-245">Para obter mais informações sobre as integrações `IHttpClientFactory` e Polly, consulte o [wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="d2bc3-246">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d2bc3-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="d2bc3-247">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="d2bc3-248">Um <xref:System.Net.Http.HttpMessageHandler> é criado por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="d2bc3-249">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="d2bc3-250">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="d2bc3-251">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="d2bc3-252">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="d2bc3-253">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="d2bc3-254">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reajam às alterações de DNS (sistema de nomes de domínio).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="d2bc3-255">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="d2bc3-256">O valor padrão pode ser substituído em uma base de cliente por nome:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="d2bc3-257">instâncias de `HttpClient` geralmente podem ser tratadas como objetos .NET que **não** exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="d2bc3-258">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="d2bc3-259">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="d2bc3-260">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="d2bc3-261">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="d2bc3-262">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="d2bc3-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="d2bc3-263">O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="d2bc3-264">Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="d2bc3-265">Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-265">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="d2bc3-266">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="d2bc3-267">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="d2bc3-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="d2bc3-269">Crie `HttpClient` instâncias usando `new HttpClient(handler, dispostHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-269">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="d2bc3-270">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="d2bc3-271">O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-272">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="d2bc3-273">O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-273">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="d2bc3-274">Cookies</span><span class="sxs-lookup"><span data-stu-id="d2bc3-274">Cookies</span></span>

<span data-ttu-id="d2bc3-275">As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="d2bc3-276">O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="d2bc3-277">Para aplicativos que exigem cookies, considere o:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="d2bc3-278">Desabilitando a manipulação automática de cookies</span><span class="sxs-lookup"><span data-stu-id="d2bc3-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="d2bc3-279">Evitando `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="d2bc3-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="d2bc3-280">Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="d2bc3-281">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="d2bc3-281">Logging</span></span>

<span data-ttu-id="d2bc3-282">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="d2bc3-283">Habilite o nível de informações apropriado na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="d2bc3-284">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="d2bc3-285">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="d2bc3-286">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de "System .net. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="d2bc3-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="d2bc3-287">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="d2bc3-288">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="d2bc3-289">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="d2bc3-290">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="d2bc3-291">No exemplo de *MyNamedClient* , essas mensagens são registradas com a categoria de log "System .net. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="d2bc3-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="d2bc3-292">Para a solicitação, isso ocorre depois que todos os outros manipuladores forem executados e imediatamente antes de a solicitação ser enviada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="d2bc3-293">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="d2bc3-294">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="d2bc3-295">Isso pode incluir alterações nos cabeçalhos de solicitação ou no código de status de resposta.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="d2bc3-296">A inclusão do nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="d2bc3-297">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="d2bc3-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="d2bc3-298">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="d2bc3-299">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="d2bc3-300">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="d2bc3-301">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="d2bc3-302">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="d2bc3-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="d2bc3-303">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="d2bc3-304">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="d2bc3-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="d2bc3-305">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="d2bc3-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="d2bc3-306">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-306">In the following example:</span></span>

* <span data-ttu-id="d2bc3-307"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="d2bc3-308">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="d2bc3-309">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="d2bc3-310">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="d2bc3-311">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d2bc3-311">Additional resources</span></span>

* <span data-ttu-id="d2bc3-312">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="d2bc3-312">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="d2bc3-313">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="d2bc3-314">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="d2bc3-314">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="d2bc3-315">Como serializar e desserializar JSON no .NET</span><span class="sxs-lookup"><span data-stu-id="d2bc3-315">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d2bc3-316">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="d2bc3-316">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="d2bc3-317">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-317">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="d2bc3-318">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-318">It offers the following benefits:</span></span>

* <span data-ttu-id="d2bc3-319">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-319">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-320">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-320">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="d2bc3-321">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-321">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="d2bc3-322">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-322">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="d2bc3-323">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-323">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d2bc3-324">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-324">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d2bc3-325">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2bc3-325">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="d2bc3-326">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-326">Consumption patterns</span></span>

<span data-ttu-id="d2bc3-327">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-327">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="d2bc3-328">Uso básico</span><span class="sxs-lookup"><span data-stu-id="d2bc3-328">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="d2bc3-329">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-329">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="d2bc3-330">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-330">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="d2bc3-331">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-331">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="d2bc3-332">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-332">None of them are strictly superior to another.</span></span> <span data-ttu-id="d2bc3-333">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-333">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="d2bc3-334">Uso básico</span><span class="sxs-lookup"><span data-stu-id="d2bc3-334">Basic usage</span></span>

<span data-ttu-id="d2bc3-335">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-335">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="d2bc3-336">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-336">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d2bc3-337">O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-337">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="d2bc3-338">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-338">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="d2bc3-339">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-339">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="d2bc3-340">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-340">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="d2bc3-341">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-341">Named clients</span></span>

<span data-ttu-id="d2bc3-342">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-342">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="d2bc3-343">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-343">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="d2bc3-344">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-344">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="d2bc3-345">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-345">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="d2bc3-346">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-346">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="d2bc3-347">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-347">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="d2bc3-348">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-348">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="d2bc3-349">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-349">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="d2bc3-350">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-350">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="d2bc3-351">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-351">Typed clients</span></span>

<span data-ttu-id="d2bc3-352">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-352">Typed clients:</span></span>

* <span data-ttu-id="d2bc3-353">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-353">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="d2bc3-354">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-354">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="d2bc3-355">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-355">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="d2bc3-356">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-356">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="d2bc3-357">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-357">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="d2bc3-358">Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-358">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="d2bc3-359">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-359">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="d2bc3-360">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-360">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="d2bc3-361">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-361">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="d2bc3-362">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-362">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="d2bc3-363">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-363">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="d2bc3-364">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-364">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="d2bc3-365">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-365">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="d2bc3-366">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-366">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="d2bc3-367">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-367">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="d2bc3-368">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-368">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="d2bc3-369">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-369">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="d2bc3-370">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-370">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="d2bc3-371">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-371">Generated clients</span></span>

<span data-ttu-id="d2bc3-372">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-372">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="d2bc3-373">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-373">Refit is a REST library for .NET.</span></span> <span data-ttu-id="d2bc3-374">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-374">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="d2bc3-375">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-375">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="d2bc3-376">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-376">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="d2bc3-377">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-377">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="d2bc3-378">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-378">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="d2bc3-379">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="d2bc3-379">Outgoing request middleware</span></span>

<span data-ttu-id="d2bc3-380">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-380">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="d2bc3-381">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-381">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="d2bc3-382">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-382">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d2bc3-383">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-383">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="d2bc3-384">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-384">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="d2bc3-385">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-385">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="d2bc3-386">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-386">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="d2bc3-387">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-387">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="d2bc3-388">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-388">The preceding code defines a basic handler.</span></span> <span data-ttu-id="d2bc3-389">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-389">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="d2bc3-390">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-390">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="d2bc3-391">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração para um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-391">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="d2bc3-392">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-392">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="d2bc3-393">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-393">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="d2bc3-394">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-394">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="d2bc3-395">Os manipuladores podem depender de serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-395">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="d2bc3-396">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-396">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="d2bc3-397">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-397">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="d2bc3-398">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-398">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="d2bc3-399">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-399">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="d2bc3-400">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-400">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="d2bc3-401">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-401">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="d2bc3-402">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-402">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="d2bc3-403">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-403">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="d2bc3-404">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-404">Use Polly-based handlers</span></span>

<span data-ttu-id="d2bc3-405">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-405">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="d2bc3-406">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-406">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="d2bc3-407">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-407">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="d2bc3-408">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-408">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-409">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-409">The Polly extensions:</span></span>

* <span data-ttu-id="d2bc3-410">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-410">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="d2bc3-411">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-411">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="d2bc3-412">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-412">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="d2bc3-413">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="d2bc3-413">Handle transient faults</span></span>

<span data-ttu-id="d2bc3-414">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-414">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="d2bc3-415">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-415">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="d2bc3-416">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-416">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="d2bc3-417">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-417">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d2bc3-418">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-418">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="d2bc3-419">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-419">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="d2bc3-420">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-420">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="d2bc3-421">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="d2bc3-421">Dynamically select policies</span></span>

<span data-ttu-id="d2bc3-422">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-422">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="d2bc3-423">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-423">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="d2bc3-424">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-424">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="d2bc3-425">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-425">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="d2bc3-426">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-426">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="d2bc3-427">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-427">Add multiple Polly handlers</span></span>

<span data-ttu-id="d2bc3-428">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-428">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="d2bc3-429">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-429">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="d2bc3-430">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-430">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="d2bc3-431">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-431">Failed requests are retried up to three times.</span></span> <span data-ttu-id="d2bc3-432">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-432">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="d2bc3-433">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-433">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="d2bc3-434">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-434">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="d2bc3-435">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-435">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="d2bc3-436">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-436">Add policies from the Polly registry</span></span>

<span data-ttu-id="d2bc3-437">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-437">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="d2bc3-438">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-438">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="d2bc3-439">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-439">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="d2bc3-440">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-440">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="d2bc3-441">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-441">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="d2bc3-442">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d2bc3-442">HttpClient and lifetime management</span></span>

<span data-ttu-id="d2bc3-443">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-443">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="d2bc3-444">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-444">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="d2bc3-445">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-445">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="d2bc3-446">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-446">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="d2bc3-447">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-447">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="d2bc3-448">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-448">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="d2bc3-449">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-449">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="d2bc3-450">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-450">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="d2bc3-451">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-451">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="d2bc3-452">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-452">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="d2bc3-453">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-453">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="d2bc3-454">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-454">Disposal of the client isn't required.</span></span> <span data-ttu-id="d2bc3-455">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-455">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="d2bc3-456">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-456">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-457">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-457">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="d2bc3-458">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-458">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="d2bc3-459">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-459">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="d2bc3-460">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="d2bc3-460">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="d2bc3-461">O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-461">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="d2bc3-462">Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-462">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="d2bc3-463">Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-463">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="d2bc3-464">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-464">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="d2bc3-465">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-465">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="d2bc3-466">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-466">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="d2bc3-467">Crie `HttpClient` instâncias usando `new HttpClient(handler, dispostHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-467">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="d2bc3-468">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-468">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="d2bc3-469">O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-469">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-470">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-470">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="d2bc3-471">O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-471">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="d2bc3-472">Cookies</span><span class="sxs-lookup"><span data-stu-id="d2bc3-472">Cookies</span></span>

<span data-ttu-id="d2bc3-473">As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-473">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="d2bc3-474">O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-474">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="d2bc3-475">Para aplicativos que exigem cookies, considere o:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-475">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="d2bc3-476">Desabilitando a manipulação automática de cookies</span><span class="sxs-lookup"><span data-stu-id="d2bc3-476">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="d2bc3-477">Evitando `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="d2bc3-477">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="d2bc3-478">Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-478">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="d2bc3-479">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="d2bc3-479">Logging</span></span>

<span data-ttu-id="d2bc3-480">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-480">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="d2bc3-481">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-481">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="d2bc3-482">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-482">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="d2bc3-483">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-483">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="d2bc3-484">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-484">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="d2bc3-485">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-485">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="d2bc3-486">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-486">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="d2bc3-487">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-487">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="d2bc3-488">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-488">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="d2bc3-489">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-489">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="d2bc3-490">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-490">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="d2bc3-491">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-491">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="d2bc3-492">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-492">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="d2bc3-493">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-493">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="d2bc3-494">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-494">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="d2bc3-495">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="d2bc3-495">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="d2bc3-496">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-496">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="d2bc3-497">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-497">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="d2bc3-498">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-498">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="d2bc3-499">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-499">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="d2bc3-500">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="d2bc3-500">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="d2bc3-501">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-501">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="d2bc3-502">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="d2bc3-502">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="d2bc3-503">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="d2bc3-503">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="d2bc3-504">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-504">In the following example:</span></span>

* <span data-ttu-id="d2bc3-505"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-505"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="d2bc3-506">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-506">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="d2bc3-507">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-507">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="d2bc3-508">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-508">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="d2bc3-509">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d2bc3-509">Additional resources</span></span>

* <span data-ttu-id="d2bc3-510">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="d2bc3-510">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="d2bc3-511">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-511">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="d2bc3-512">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="d2bc3-512">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d2bc3-513">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="d2bc3-513">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="d2bc3-514">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-514">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="d2bc3-515">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-515">It offers the following benefits:</span></span>

* <span data-ttu-id="d2bc3-516">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-516">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-517">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-517">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="d2bc3-518">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-518">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="d2bc3-519">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-519">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="d2bc3-520">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-520">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d2bc3-521">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-521">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d2bc3-522">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d2bc3-522">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d2bc3-523">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d2bc3-523">Prerequisites</span></span>

<span data-ttu-id="d2bc3-524">Os projetos direcionados ao .NET Framework exigem a instalação do pacote do NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-524">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="d2bc3-525">Os projetos destinados ao .Net Core e a referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) já incluem o pacote `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-525">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="d2bc3-526">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-526">Consumption patterns</span></span>

<span data-ttu-id="d2bc3-527">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-527">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="d2bc3-528">Uso básico</span><span class="sxs-lookup"><span data-stu-id="d2bc3-528">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="d2bc3-529">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-529">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="d2bc3-530">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-530">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="d2bc3-531">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-531">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="d2bc3-532">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-532">None of them are strictly superior to another.</span></span> <span data-ttu-id="d2bc3-533">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-533">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="d2bc3-534">Uso básico</span><span class="sxs-lookup"><span data-stu-id="d2bc3-534">Basic usage</span></span>

<span data-ttu-id="d2bc3-535">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-535">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="d2bc3-536">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-536">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d2bc3-537">O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-537">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="d2bc3-538">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-538">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="d2bc3-539">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-539">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="d2bc3-540">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-540">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="d2bc3-541">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-541">Named clients</span></span>

<span data-ttu-id="d2bc3-542">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-542">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="d2bc3-543">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-543">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="d2bc3-544">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-544">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="d2bc3-545">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-545">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="d2bc3-546">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-546">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="d2bc3-547">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-547">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="d2bc3-548">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-548">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="d2bc3-549">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-549">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="d2bc3-550">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-550">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="d2bc3-551">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="d2bc3-551">Typed clients</span></span>

<span data-ttu-id="d2bc3-552">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-552">Typed clients:</span></span>

* <span data-ttu-id="d2bc3-553">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-553">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="d2bc3-554">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-554">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="d2bc3-555">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-555">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="d2bc3-556">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-556">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="d2bc3-557">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-557">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="d2bc3-558">Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-558">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="d2bc3-559">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-559">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="d2bc3-560">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-560">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="d2bc3-561">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-561">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="d2bc3-562">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-562">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="d2bc3-563">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-563">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="d2bc3-564">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-564">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="d2bc3-565">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-565">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="d2bc3-566">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-566">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="d2bc3-567">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-567">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="d2bc3-568">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-568">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="d2bc3-569">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-569">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="d2bc3-570">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-570">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="d2bc3-571">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="d2bc3-571">Generated clients</span></span>

<span data-ttu-id="d2bc3-572">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-572">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="d2bc3-573">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-573">Refit is a REST library for .NET.</span></span> <span data-ttu-id="d2bc3-574">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-574">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="d2bc3-575">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-575">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="d2bc3-576">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-576">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="d2bc3-577">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-577">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="d2bc3-578">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-578">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="d2bc3-579">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="d2bc3-579">Outgoing request middleware</span></span>

<span data-ttu-id="d2bc3-580">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-580">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="d2bc3-581">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-581">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="d2bc3-582">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-582">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d2bc3-583">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-583">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="d2bc3-584">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-584">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="d2bc3-585">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-585">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="d2bc3-586">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-586">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="d2bc3-587">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-587">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="d2bc3-588">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-588">The preceding code defines a basic handler.</span></span> <span data-ttu-id="d2bc3-589">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-589">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="d2bc3-590">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-590">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="d2bc3-591">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração para um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-591">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="d2bc3-592">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-592">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="d2bc3-593">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-593">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="d2bc3-594">O manipulador **deve** ser registrado na injeção de dependência como serviço temporário, mas nunca com escopo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-594">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="d2bc3-595">Se o manipulador estiver registrado como um serviço com escopo e algum serviço do qual ele depende for descartável:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-595">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="d2bc3-596">Os serviços do manipulador poderão ser descartados antes que ele saia do escopo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-596">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="d2bc3-597">Os serviços de manipulador descartados farão com que o manipulador falhe.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-597">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="d2bc3-598">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando no tipo do manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-598">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="d2bc3-599">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-599">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="d2bc3-600">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-600">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="d2bc3-601">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-601">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="d2bc3-602">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-602">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="d2bc3-603">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-603">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="d2bc3-604">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-604">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="d2bc3-605">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-605">Use Polly-based handlers</span></span>

<span data-ttu-id="d2bc3-606">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-606">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="d2bc3-607">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-607">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="d2bc3-608">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-608">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="d2bc3-609">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-609">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-610">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-610">The Polly extensions:</span></span>

* <span data-ttu-id="d2bc3-611">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-611">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="d2bc3-612">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-612">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="d2bc3-613">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-613">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="d2bc3-614">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="d2bc3-614">Handle transient faults</span></span>

<span data-ttu-id="d2bc3-615">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-615">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="d2bc3-616">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-616">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="d2bc3-617">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-617">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="d2bc3-618">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-618">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d2bc3-619">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-619">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="d2bc3-620">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-620">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="d2bc3-621">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-621">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="d2bc3-622">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="d2bc3-622">Dynamically select policies</span></span>

<span data-ttu-id="d2bc3-623">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-623">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="d2bc3-624">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-624">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="d2bc3-625">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-625">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="d2bc3-626">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-626">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="d2bc3-627">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-627">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="d2bc3-628">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-628">Add multiple Polly handlers</span></span>

<span data-ttu-id="d2bc3-629">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-629">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="d2bc3-630">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-630">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="d2bc3-631">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-631">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="d2bc3-632">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-632">Failed requests are retried up to three times.</span></span> <span data-ttu-id="d2bc3-633">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-633">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="d2bc3-634">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-634">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="d2bc3-635">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-635">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="d2bc3-636">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-636">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="d2bc3-637">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-637">Add policies from the Polly registry</span></span>

<span data-ttu-id="d2bc3-638">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-638">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="d2bc3-639">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-639">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="d2bc3-640">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-640">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="d2bc3-641">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-641">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="d2bc3-642">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-642">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="d2bc3-643">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="d2bc3-643">HttpClient and lifetime management</span></span>

<span data-ttu-id="d2bc3-644">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-644">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="d2bc3-645">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-645">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="d2bc3-646">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-646">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="d2bc3-647">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-647">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="d2bc3-648">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-648">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="d2bc3-649">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-649">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="d2bc3-650">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-650">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="d2bc3-651">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-651">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="d2bc3-652">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-652">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="d2bc3-653">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-653">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="d2bc3-654">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-654">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="d2bc3-655">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-655">Disposal of the client isn't required.</span></span> <span data-ttu-id="d2bc3-656">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-656">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="d2bc3-657">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-657">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-658">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-658">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="d2bc3-659">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-659">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="d2bc3-660">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-660">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="d2bc3-661">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="d2bc3-661">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="d2bc3-662">O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-662">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="d2bc3-663">Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-663">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="d2bc3-664">Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-664">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="d2bc3-665">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-665">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="d2bc3-666">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-666">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="d2bc3-667">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-667">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="d2bc3-668">Crie `HttpClient` instâncias usando `new HttpClient(handler, dispostHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-668">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="d2bc3-669">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-669">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="d2bc3-670">O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-670">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="d2bc3-671">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-671">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="d2bc3-672">O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-672">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="d2bc3-673">Cookies</span><span class="sxs-lookup"><span data-stu-id="d2bc3-673">Cookies</span></span>

<span data-ttu-id="d2bc3-674">As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-674">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="d2bc3-675">O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-675">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="d2bc3-676">Para aplicativos que exigem cookies, considere o:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-676">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="d2bc3-677">Desabilitando a manipulação automática de cookies</span><span class="sxs-lookup"><span data-stu-id="d2bc3-677">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="d2bc3-678">Evitando `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="d2bc3-678">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="d2bc3-679">Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-679">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="d2bc3-680">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="d2bc3-680">Logging</span></span>

<span data-ttu-id="d2bc3-681">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-681">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="d2bc3-682">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-682">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="d2bc3-683">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-683">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="d2bc3-684">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-684">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="d2bc3-685">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-685">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="d2bc3-686">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-686">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="d2bc3-687">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-687">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="d2bc3-688">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-688">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="d2bc3-689">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-689">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="d2bc3-690">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-690">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="d2bc3-691">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-691">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="d2bc3-692">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-692">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="d2bc3-693">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-693">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="d2bc3-694">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-694">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="d2bc3-695">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-695">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="d2bc3-696">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="d2bc3-696">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="d2bc3-697">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-697">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="d2bc3-698">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-698">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="d2bc3-699">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-699">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="d2bc3-700">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-700">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="d2bc3-701">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="d2bc3-701">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="d2bc3-702">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-702">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="d2bc3-703">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="d2bc3-703">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="d2bc3-704">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="d2bc3-704">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="d2bc3-705">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d2bc3-705">In the following example:</span></span>

* <span data-ttu-id="d2bc3-706"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d2bc3-706"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="d2bc3-707">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-707">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="d2bc3-708">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-708">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="d2bc3-709">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="d2bc3-709">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="d2bc3-710">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d2bc3-710">Additional resources</span></span>

* <span data-ttu-id="d2bc3-711">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="d2bc3-711">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="d2bc3-712">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="d2bc3-712">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="d2bc3-713">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="d2bc3-713">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
