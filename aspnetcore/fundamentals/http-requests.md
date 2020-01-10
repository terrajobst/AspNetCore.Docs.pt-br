---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 482f8e28c23c621cecaf9ce111d89e9166ea6d85
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722720"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Kirk Larkin](https://github.com/serpent5)

É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo. o `IHttpClientFactory` oferece os seguintes benefícios:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, um cliente chamado *GitHub* pode ser registrado e configurado para acessar o [GitHub](https://github.com/). Um cliente padrão pode ser registrado para acesso geral.
* Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient`. Fornece extensões para o middleware baseado em Polly para tirar proveito da delegação de manipuladores no `HttpClient`.
* Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes. O gerenciamento automático evita problemas comuns de DNS (sistema de nomes de domínio) que ocorrem ao gerenciar manualmente `HttpClient` tempos de vida.
* Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample)).

O código de exemplo nesta versão de tópico usa <xref:System.Text.Json> para desserializar o conteúdo JSON retornado em respostas HTTP. Para obter exemplos que usam `Json.NET` e `ReadAsAsync<T>`, use o seletor de versão para selecionar uma versão 2. x deste tópico.

## <a name="consumption-patterns"></a>Padrões de consumo

Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:

* [Uso básico](#basic-usage)
* [Clientes nomeados](#named-clients)
* [Clientes com tipo](#typed-clients)
* [Clientes gerados](#generated-clients)

A melhor abordagem depende dos requisitos do aplicativo.

### <a name="basic-usage"></a>Uso básico

`IHttpClientFactory` pode ser registrado chamando `AddHttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Um `IHttpClientFactory` pode ser solicitado usando [injeção de dependência (di)](xref:fundamentals/dependency-injection). O código a seguir usa `IHttpClientFactory` para criar uma instância de `HttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Usar `IHttpClientFactory` como no exemplo anterior é uma boa maneira de refatorar um aplicativo existente. Ele não tem impacto sobre como `HttpClient` é usado. Em locais onde `HttpClient` instâncias são criadas em um aplicativo existente, substitua essas ocorrências por chamadas para <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Clientes nomeados

Os clientes nomeados são uma boa opção quando:

* O aplicativo requer muitos usos distintos de `HttpClient`.
* Muitos `HttpClient`s têm configuração diferente.

A configuração para um `HttpClient` nomeado pode ser especificada durante o registro no `Startup.ConfigureServices`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

No código anterior, o cliente está configurado com:

* O endereço base `https://api.github.com/`.
* Dois cabeçalhos necessários para trabalhar com a API do GitHub.

#### <a name="createclient"></a>CreateClient

Cada vez que <xref:System.Net.Http.IHttpClientFactory.CreateClient*> é chamado:

* Uma nova instância do `HttpClient` é criada.
* A ação de configuração é chamada.

Para criar um cliente nomeado, passe seu nome para `CreateClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

No código anterior, a solicitação não precisa especificar um nome do host. O código pode passar apenas o caminho, já que o endereço base configurado para o cliente é usado.

### <a name="typed-clients"></a>Clientes com tipo

Clientes com tipo:

* Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.
* Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.
* Fornecem um único local para configurar e interagir com um determinado `HttpClient`. Por exemplo, um único cliente tipado pode ser usado:
  * Para um ponto de extremidade de back-end único.
  * Para encapsular toda a lógica lidando com o ponto de extremidade.
* Trabalhe com DI e pode ser injetado onde necessário no aplicativo.

Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

No código anterior:

* A configuração é movida para o cliente digitado.
* O objeto `HttpClient` é exposto como uma propriedade pública.

Métodos específicos de API podem ser criados para expor `HttpClient` funcionalidade. Por exemplo, o método `GetAspNetDocsIssues` encapsula o código para recuperar problemas abertos.

O código a seguir chama <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> em `Startup.ConfigureServices` para registrar uma classe de cliente tipada:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

O cliente com tipo é registrado como transitório com a DI. O cliente com tipo pode ser injetado e consumido diretamente:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

A configuração de um cliente tipado pode ser especificada durante o registro no `Startup.ConfigureServices`, em vez de no construtor do cliente digitado:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

O `HttpClient` pode ser encapsulado em um cliente digitado. Em vez de expô-lo como uma propriedade, defina um método que chame a instância de `HttpClient` internamente:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

No código anterior, a `HttpClient` é armazenada em um campo particular. O acesso ao `HttpClient` é pelo método `GetRepos` público.

### <a name="generated-clients"></a>Clientes gerados

`IHttpClientFactory` pode ser usado em combinação com bibliotecas de terceiros, como [rEFIt](https://github.com/paulcbetts/refit). Refit é uma biblioteca REST para .NET. Ela converte APIs REST em interfaces dinâmicas. Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.

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

`HttpClient` tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída. `IHttpClientFactory`:

* Simplifica a definição dos manipuladores a serem aplicados para cada cliente nomeado.
* Dá suporte ao registro e encadeamento de vários manipuladores para criar um pipeline de middleware de solicitação de saída. Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída. Esse padrão:

  * É semelhante ao pipeline de middleware de entrada no ASP.NET Core.
  * Fornece um mecanismo para gerenciar preocupações abrangentes em relação a solicitações HTTP, como:

    * caching
    * tratamento de erros
    * serialização
    * log

Para criar um manipulador de delegação:

* Derive de <xref:System.Net.Http.DelegatingHandler>.
* Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>. Execute o código antes de passar a solicitação para o próximo manipulador no pipeline:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

O código anterior verifica se o cabeçalho de `X-API-KEY` está na solicitação. Se `X-API-KEY` estiver ausente, <xref:System.Net.HttpStatusCode.BadRequest> será retornado.

Mais de um manipulador pode ser adicionado à configuração para um `HttpClient` com <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

No código anterior, o `ValidateHeaderHandler` é registrado com a DI. O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador. Os manipuladores podem depender dos serviços de qualquer escopo. Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.

Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.

Vários manipuladores podem ser registrados na ordem em que eles devem ser executados. Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:

* Passe dados para o manipulador usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.
* Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.

## <a name="use-polly-based-handlers"></a>Usar manipuladores baseados no Polly

o `IHttpClientFactory` integra-se com a biblioteca de terceiros [Polly](https://github.com/App-vNext/Polly). O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET. Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.

Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas. As extensões Polly dão suporte à adição de manipuladores baseados em Polly a clientes. Polly requer o pacote NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .

### <a name="handle-transient-faults"></a>Tratar falhas transitórias

As falhas normalmente ocorrem quando chamadas HTTP externas são transitórias. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permite que uma política seja definida para lidar com erros transitórios. As políticas configuradas com `AddTransientHttpErrorPolicy` manipulam as seguintes respostas:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy` fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

No código anterior, uma política `WaitAndRetryAsync` é definida. As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.

### <a name="dynamically-select-policies"></a>Selecionar políticas dinamicamente

Os métodos de extensão são fornecidos para adicionar manipuladores baseados em Polly, por exemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>. A sobrecarga de `AddPolicyHandler` a seguir inspeciona a solicitação para decidir qual política aplicar:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado. Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.

### <a name="add-multiple-polly-handlers"></a>Adicionar vários manipuladores do Polly

É comum aninhar políticas Polly:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

No exemplo anterior:

* Dois manipuladores são adicionados.
* O primeiro manipulador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para adicionar uma política de repetição. As solicitações com falha são repetidas até três vezes.
* A segunda chamada de `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor. Outras solicitações externas serão bloqueadas por 30 segundos se 5 tentativas com falha ocorrerem em sequência. As políticas de disjuntor são políticas com estado. Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.

### <a name="add-policies-from-the-polly-registry"></a>Adicionar políticas do registro do Polly

Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.

No seguinte código:

* As políticas "regular" e "longa" são adicionadas.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adiciona as políticas "regular" e "longa" do registro.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Para obter mais informações sobre as integrações `IHttpClientFactory` e Polly, consulte o [wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient e gerenciamento de tempo de vida

Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`. Um <xref:System.Net.Http.HttpMessageHandler> é criado por cliente nomeado. O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.

`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos. Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.

O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes. Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão. Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reajam às alterações de DNS (sistema de nomes de domínio).

O tempo de vida padrão do manipulador é de 2 minutos. O valor padrão pode ser substituído em uma base de cliente por nome:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

instâncias de `HttpClient` geralmente podem ser tratadas como objetos .NET que **não** exigem descarte. O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.

Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`. Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativas para IHttpClientFactory

O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:

* Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.
* Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.

Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.

- Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.
- Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.

As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.

- O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`. Esse compartilhamento impede o esgotamento de soquete.
- O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.

### <a name="cookies"></a>Cookies

As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados. O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto. Para aplicativos que exigem cookies, considere o:

 - Desabilitando a manipulação automática de cookies
 - Evitando `IHttpClientFactory`

Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Registrando em log

Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações. Habilite o nível de informações apropriado na configuração de log para ver as mensagens de log padrão. Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.

A categoria de log usada para cada cliente inclui o nome do cliente. Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de "System .net. http. HttpClient. **MyNamedClient**. LogicalHandler". Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações. Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado. Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.

O registro em log também ocorre dentro do pipeline do manipulador de solicitações. No exemplo de *MyNamedClient* , essas mensagens são registradas com a categoria de log "System .net. http. HttpClient. **MyNamedClient**. ClientHandler". Para a solicitação, isso ocorre depois que todos os outros manipuladores forem executados e imediatamente antes de a solicitação ser enviada. Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.

Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline. Isso pode incluir alterações nos cabeçalhos de solicitação ou no código de status de resposta.

A inclusão do nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos.

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
* [Como serializar e desserializar JSON no .NET](/dotnet/standard/serialization/system-text-json-how-to)

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

Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:

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

Durante o registro, um ou mais manipuladores podem ser adicionados à configuração para um `HttpClient`. Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

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

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativas para IHttpClientFactory

O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:

* Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.
* Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.

Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.

- Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.
- Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.

As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.

- O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`. Esse compartilhamento impede o esgotamento de soquete.
- O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.

### <a name="cookies"></a>Cookies

As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados. O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto. Para aplicativos que exigem cookies, considere o:

 - Desabilitando a manipulação automática de cookies
 - Evitando `IHttpClientFactory`

Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:

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

::: moniker range="= aspnetcore-2.1"

Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)

É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo. Ele oferece os seguintes benefícios:

* Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`. Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/). Um cliente padrão pode ser registrado para outras finalidades.
* Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.
* Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.
* Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

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

Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:

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

Durante o registro, um ou mais manipuladores podem ser adicionados à configuração para um `HttpClient`. Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

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

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativas para IHttpClientFactory

O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:

* Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.
* Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.

Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.

- Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.
- Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.
- Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.

As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.

- O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`. Esse compartilhamento impede o esgotamento de soquete.
- O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.

### <a name="cookies"></a>Cookies

As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados. O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto. Para aplicativos que exigem cookies, considere o:

 - Desabilitando a manipulação automática de cookies
 - Evitando `IHttpClientFactory`

Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:

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
