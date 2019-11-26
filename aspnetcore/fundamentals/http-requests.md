---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 7a5b5c84775ea2482034ef9f3e8a2376036e66cb
ms.sourcegitcommit: a104ba258ae7c0b3ee7c6fa7eaea1ddeb8b6eb73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74478742"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)

É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo. `IHttpClientFactory` offers the following benefits:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/). A default client can be registered for general access.
* Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`. Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.
* Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances. Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.
* Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample)).

The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses. For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.

## <a name="consumption-patterns"></a>Padrões de consumo

Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:

* [Uso básico](#basic-usage)
* [Clientes nomeados](#named-clients)
* [Clientes com tipo](#typed-clients)
* [Clientes gerados](#generated-clients)

The best approach depends upon the app's requirements.

### <a name="basic-usage"></a>Uso básico

`IHttpClientFactory` can be registered by calling `AddHttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection). The following code uses `IHttpClientFactory` to create an `HttpClient` instance:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app. It has no impact on how `HttpClient` is used. In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clientes nomeados

Named clients are a good choice when:

* The app requires many distinct uses of `HttpClient`.
* Many `HttpClient`s have different configuration.

Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

In the preceding code the client is configured with:

* The base address `https://api.github.com/`.
* Two headers required to work with the GitHub API.

#### <a name="createclient"></a>CreateClient

Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:

* A new instance of `HttpClient` is created.
* The configuration action is called.

To create a named client, pass its name into `CreateClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

No código anterior, a solicitação não precisa especificar um nome do host. The code can pass just the path, since the base address configured for the client is used.

### <a name="typed-clients"></a>Clientes com tipo

Clientes com tipo:

* Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.
* Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.
* Fornecem um único local para configurar e interagir com um determinado `HttpClient`. For example, a single typed client might be used:
  * For a single backend endpoint.
  * To encapsulate all logic dealing with the endpoint.
* Work with DI and can be injected where required in the app.

Um cliente com tipo aceita um parâmetro `HttpClient` em seu construtor:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

No código anterior:

* The configuration is moved into the typed client.
* O objeto `HttpClient` é exposto como uma propriedade pública.

API-specific methods can be created that expose `HttpClient` functionality. For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.

The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

O cliente com tipo é registrado como transitório com a DI. O cliente com tipo pode ser injetado e consumido diretamente:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

The `HttpClient` can be encapsulated within a typed client. Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

In the preceding code, the `HttpClient` is stored in a private field. Access to the `HttpClient` is by the public `GetRepos` method.

### <a name="generated-clients"></a>Clientes gerados

`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit). Refit é uma biblioteca REST para .NET. Ela converte APIs REST em interfaces dinâmicas. Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.

Uma interface e uma resposta são definidas para representar a API externa e sua resposta:

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

Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:

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

A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:

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

## <a name="outgoing-request-middleware"></a>Middleware de solicitação de saída

`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests. `IHttpClientFactory`:

* Simplifies defining the handlers to apply for each named client.
* Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline. Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída. This pattern:

  * Is similar to the inbound middleware pipeline in ASP.NET Core.
  * Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:

    * cache
    * tratamento de erros
    * serialização
    * log

To create a delegating handler:

* Derive from <xref:System.Net.Http.DelegatingHandler>.
* Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>. Execute code before passing the request to the next handler in the pipeline:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

The preceding code checks if the `X-API-KEY` header is in the request. If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.

More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

No código anterior, o `ValidateHeaderHandler` é registrado com a DI. O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador. Handlers can depend upon services of any scope. Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.

Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.

Vários manipuladores podem ser registrados na ordem em que eles devem ser executados. Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:

* Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.
* Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.

## <a name="use-polly-based-handlers"></a>Usar manipuladores baseados no Polly

`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly). O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET. Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.

Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas. The Polly extensions support adding Polly-based handlers to clients. Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.

### <a name="handle-transient-faults"></a>Tratar falhas transitórias

Faults typically occur when external HTTP calls are transient. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors. Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

No código anterior, uma política `WaitAndRetryAsync` é definida. As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.

### <a name="dynamically-select-policies"></a>Selecionar políticas dinamicamente

Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>. The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado. Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.

### <a name="add-multiple-polly-handlers"></a>Adicionar vários manipuladores do Polly

It's common to nest Polly policies:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

No exemplo anterior:

* Two handlers are added.
* The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy. As solicitações com falha são repetidas até três vezes.
* The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy. Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially. As políticas de disjuntor são políticas com estado. Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.

### <a name="add-policies-from-the-polly-registry"></a>Adicionar políticas do registro do Polly

Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.

No seguinte código:

* The "regular" and "long" polices are added.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient e gerenciamento de tempo de vida

Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`. An <xref:System.Net.Http.HttpMessageHandler> is created per named client. O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.

`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos. Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.

O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes. Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão. Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.

O tempo de vida padrão do manipulador é de 2 minutos. The default value can be overridden on a per named client basis:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal. O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.

Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`. Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatives to IHttpClientFactory

Using `IHttpClientFactory` in a DI-enabled app avoids:

* Resource exhaustion problems by pooling `HttpMessageHandler` instances.
* Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.

There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.

- Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.
- Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.

The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.

- The `SocketsHttpHandler` shares connections across `HttpClient` instances. This sharing prevents socket exhaustion.
- The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.

### <a name="cookies"></a>Cookies

The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared. Unanticipated `CookieContainer` object sharing often results in incorrect code. For apps that require cookies, consider either:

 - Disabling automatic cookie handling
 - Avoiding `IHttpClientFactory`

Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Registrando em log

Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações. Enable the appropriate information level in the logging configuration to see the default log messages. Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.

A categoria de log usada para cada cliente inclui o nome do cliente. A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler". Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações. Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado. Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.

O registro em log também ocorre dentro do pipeline do manipulador de solicitações. In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler". For the request, this occurs after all other handlers have run and immediately before the request is sent. Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.

Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline. This may include changes to request headers or to the response status code.

Including the name of the client in the log category enables log filtering for specific named clients.

## <a name="configure-the-httpmessagehandler"></a>Configurar o HttpMessageHandler

Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.

Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo. O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante. O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Usar IHttpClientFactory em um aplicativo de console

Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

No exemplo a seguir:

* <xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).
* `MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`. `HttpClient` é usado para recuperar uma página da Web.
* `Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Recursos adicionais

* [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)
* [Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementar o padrão de disjuntor](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)

É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo. Ele oferece os seguintes benefícios:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/). Um cliente padrão pode ser registrado para outras finalidades.
* Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.
* Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.
* Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Padrões de consumo

Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:

* [Uso básico](#basic-usage)
* [Clientes nomeados](#named-clients)
* [Clientes com tipo](#typed-clients)
* [Clientes gerados](#generated-clients)

Nenhum deles é estritamente superiores ao outro. A melhor abordagem depende das restrições do aplicativo.

### <a name="basic-usage"></a>Uso básico

O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection). O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente. Não há nenhum impacto na maneira em que o `HttpClient` é usado. Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clientes nomeados

Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**. A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*. Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.

Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.

Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`. Especifique o nome do cliente a ser criado:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

No código anterior, a solicitação não precisa especificar um nome do host. Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.

### <a name="typed-clients"></a>Clientes com tipo

Clientes com tipo:

* Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.
* Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.
* Fornecem um único local para configurar e interagir com um determinado `HttpClient`. Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.
* Funcionam com a DI e podem ser injetados no local necessário no aplicativo.

Um cliente com tipo aceita um parâmetro `HttpClient` em seu construtor:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

No código anterior, a configuração é movida para o cliente com tipo. O objeto `HttpClient` é exposto como uma propriedade pública. É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`. O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.

Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

O cliente com tipo é registrado como transitório com a DI. O cliente com tipo pode ser injetado e consumido diretamente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo. Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

No código anterior, o `HttpClient` é armazenado como um campo privado. Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.

### <a name="generated-clients"></a>Clientes gerados

O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit). Refit é uma biblioteca REST para .NET. Ela converte APIs REST em interfaces dinâmicas. Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.

Uma interface e uma resposta são definidas para representar a API externa e sua resposta:

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

Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:

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

A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:

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

## <a name="outgoing-request-middleware"></a>Middleware de solicitação de saída

O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída. O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado. Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída. Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída. Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core. O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.

Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>. Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

O código anterior define um manipulador básico. Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação. Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.

Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient`. Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

No código anterior, o `ValidateHeaderHandler` é registrado com a DI. O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador. Os manipuladores podem depender de serviços de qualquer escopo. Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.

Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.

Vários manipuladores podem ser registrados na ordem em que eles devem ser executados. Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:

* Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.
* Use `IHttpContextAccessor` para acessar a solicitação atual.
* Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.

## <a name="use-polly-based-handlers"></a>Usar manipuladores baseados no Polly

O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly). O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET. Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.

Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas. As extensões do Polly:

* Dão suporte à adição de manipuladores baseados no Polly aos clientes.
* Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/). O pacote não está incluído na estrutura ASP.NET Core compartilhada.

### <a name="handle-transient-faults"></a>Tratar falhas transitórias

As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias. Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios. As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.

A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`. A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

No código anterior, uma política `WaitAndRetryAsync` é definida. As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.

### <a name="dynamically-select-policies"></a>Selecionar políticas dinamicamente

Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly. Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas. Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado. Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.

### <a name="add-multiple-polly-handlers"></a>Adicionar vários manipuladores do Polly

É comum aninhar políticas do Polly para fornecer funcionalidade avançada:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

No exemplo anterior, dois manipuladores são adicionados. O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição. As solicitações com falha são repetidas até três vezes. A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor. As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente. As políticas de disjuntor são políticas com estado. Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.

### <a name="add-policies-from-the-polly-registry"></a>Adicionar políticas do registro do Polly

Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`. É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`. Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.

Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient e gerenciamento de tempo de vida

Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`. Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado. O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.

`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos. Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.

O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes. Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão. Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.

O tempo de vida padrão do manipulador é de 2 minutos. O valor padrão pode ser substituído para cada cliente nomeado. Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Não é necessário descartar o cliente. O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`. Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.

Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`. Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatives to IHttpClientFactory

Using `IHttpClientFactory` in a DI-enabled app avoids:

* Resource exhaustion problems by pooling `HttpMessageHandler` instances.
* Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.

There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.

- Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.
- Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.

The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.

- The `SocketsHttpHandler` shares connections across `HttpClient` instances. This sharing prevents socket exhaustion.
- The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.

### <a name="cookies"></a>Cookies

The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared. Unanticipated `CookieContainer` object sharing often results in incorrect code. For apps that require cookies, consider either:

 - Disabling automatic cookie handling
 - Avoiding `IHttpClientFactory`

Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Registrando em log

Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações. Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão. Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.

A categoria de log usada para cada cliente inclui o nome do cliente. Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações. Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado. Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.

O registro em log também ocorre dentro do pipeline do manipulador de solicitações. No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede. Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.

Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline. Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.

Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.

## <a name="configure-the-httpmessagehandler"></a>Configurar o HttpMessageHandler

Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.

Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo. O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante. O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Usar IHttpClientFactory em um aplicativo de console

Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

No exemplo a seguir:

* <xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).
* `MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`. `HttpClient` é usado para recuperar uma página da Web.
* `Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Recursos adicionais

* [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)
* [Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementar o padrão de disjuntor](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)

É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo. Ele oferece os seguintes benefícios:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/). Um cliente padrão pode ser registrado para outras finalidades.
* Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.
* Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.
* Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisites

Os projetos direcionados ao .NET Framework exigem a instalação do pacote do NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/). Os projetos destinados ao .Net Core e a referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) já incluem o pacote `Microsoft.Extensions.Http`.

## <a name="consumption-patterns"></a>Padrões de consumo

Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:

* [Uso básico](#basic-usage)
* [Clientes nomeados](#named-clients)
* [Clientes com tipo](#typed-clients)
* [Clientes gerados](#generated-clients)

Nenhum deles é estritamente superiores ao outro. A melhor abordagem depende das restrições do aplicativo.

### <a name="basic-usage"></a>Uso básico

O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection). O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente. Não há nenhum impacto na maneira em que o `HttpClient` é usado. Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clientes nomeados

Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**. A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*. Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.

Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.

Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`. Especifique o nome do cliente a ser criado:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

No código anterior, a solicitação não precisa especificar um nome do host. Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.

### <a name="typed-clients"></a>Clientes com tipo

Clientes com tipo:

* Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.
* Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.
* Fornecem um único local para configurar e interagir com um determinado `HttpClient`. Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.
* Funcionam com a DI e podem ser injetados no local necessário no aplicativo.

Um cliente com tipo aceita um parâmetro `HttpClient` em seu construtor:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

No código anterior, a configuração é movida para o cliente com tipo. O objeto `HttpClient` é exposto como uma propriedade pública. É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`. O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.

Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

O cliente com tipo é registrado como transitório com a DI. O cliente com tipo pode ser injetado e consumido diretamente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo. Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

No código anterior, o `HttpClient` é armazenado como um campo privado. Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.

### <a name="generated-clients"></a>Clientes gerados

O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit). Refit é uma biblioteca REST para .NET. Ela converte APIs REST em interfaces dinâmicas. Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.

Uma interface e uma resposta são definidas para representar a API externa e sua resposta:

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

Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:

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

A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:

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

## <a name="outgoing-request-middleware"></a>Middleware de solicitação de saída

O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída. O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado. Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída. Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída. Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core. O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.

Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>. Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

O código anterior define um manipulador básico. Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação. Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.

Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient`. Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

No código anterior, o `ValidateHeaderHandler` é registrado com a DI. O manipulador **deve** ser registrado na injeção de dependência como serviço temporário, mas nunca com escopo. Se o manipulador estiver registrado como um serviço com escopo e algum serviço do qual ele depende for descartável:

* Os serviços do manipulador poderão ser descartados antes que ele saia do escopo.
* Os serviços de manipulador descartados farão com que o manipulador falhe.

Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando no tipo do manipulador.

Vários manipuladores podem ser registrados na ordem em que eles devem ser executados. Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:

* Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.
* Use `IHttpContextAccessor` para acessar a solicitação atual.
* Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.

## <a name="use-polly-based-handlers"></a>Usar manipuladores baseados no Polly

O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly). O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET. Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.

Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas. As extensões do Polly:

* Dão suporte à adição de manipuladores baseados no Polly aos clientes.
* Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/). O pacote não está incluído na estrutura ASP.NET Core compartilhada.

### <a name="handle-transient-faults"></a>Tratar falhas transitórias

As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias. Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios. As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.

A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`. A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

No código anterior, uma política `WaitAndRetryAsync` é definida. As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.

### <a name="dynamically-select-policies"></a>Selecionar políticas dinamicamente

Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly. Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas. Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado. Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.

### <a name="add-multiple-polly-handlers"></a>Adicionar vários manipuladores do Polly

É comum aninhar políticas do Polly para fornecer funcionalidade avançada:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

No exemplo anterior, dois manipuladores são adicionados. O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição. As solicitações com falha são repetidas até três vezes. A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor. As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente. As políticas de disjuntor são políticas com estado. Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.

### <a name="add-policies-from-the-polly-registry"></a>Adicionar políticas do registro do Polly

Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`. É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`. Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.

Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient e gerenciamento de tempo de vida

Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`. Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado. O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.

`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos. Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.

O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes. Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão. Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.

O tempo de vida padrão do manipulador é de 2 minutos. O valor padrão pode ser substituído para cada cliente nomeado. Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Não é necessário descartar o cliente. O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`. Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.

Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`. Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternatives to IHttpClientFactory

Using `IHttpClientFactory` in a DI-enabled app avoids:

* Resource exhaustion problems by pooling `HttpMessageHandler` instances.
* Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.

There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.

- Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.
- Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.

The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.

- The `SocketsHttpHandler` shares connections across `HttpClient` instances. This sharing prevents socket exhaustion.
- The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.

### <a name="cookies"></a>Cookies

The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared. Unanticipated `CookieContainer` object sharing often results in incorrect code. For apps that require cookies, consider either:

 - Disabling automatic cookie handling
 - Avoiding `IHttpClientFactory`

Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Registrando em log

Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações. Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão. Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.

A categoria de log usada para cada cliente inclui o nome do cliente. Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações. Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado. Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.

O registro em log também ocorre dentro do pipeline do manipulador de solicitações. No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede. Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.

Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline. Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.

Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.

## <a name="configure-the-httpmessagehandler"></a>Configurar o HttpMessageHandler

Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.

Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo. O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante. O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Usar IHttpClientFactory em um aplicativo de console

Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

No exemplo a seguir:

* <xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).
* `MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`. `HttpClient` é usado para recuperar uma página da Web.
* `Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Recursos adicionais

* [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)
* [Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementar o padrão de disjuntor](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
