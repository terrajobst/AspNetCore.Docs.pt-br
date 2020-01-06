---
title: Configuração de SignalR de ASP.NET Core
author: bradygaster
description: Saiba como configurar ASP.NET Core SignalR aplicativos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358106"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a>Configuração de SignalR de ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol` pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`. O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`. A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um objeto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> que pode ser usado para configurar a serialização de argumentos e valores de retorno. Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).

Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código no `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Não é possível configurar a serialização JSON no cliente JavaScript neste momento.

### <a name="switch-to-newtonsoftjson"></a>Alternar para Newtonsoft. JSON

Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json`, consulte [mudar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opções de serialização MessagePack

A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Consulte [MessagePack em SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve as opções para configurar os hubs de SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo. Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado. O valor recomendado é o dobro do valor de `KeepAliveInterval`.|
| `HandshakeTimeout` | 15 segundos | Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente. O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos com suporte neste Hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub. O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais. |
| `StreamBufferCapacity` | `10` | O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente. Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.|
| `MaximumReceiveMessageSize` | 32 KB | Tamanho máximo de uma única mensagem de Hub de entrada. |

As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções de configuração avançada de HTTP

Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do ASP.NET Core SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda. Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória. |
| `AuthorizationData` | Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back. Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Consulte a seguir. | Opções adicionais específicas para o transporte de sondagem longa. |
| `WebSockets` | Consulte a seguir. | Opções adicionais específicas ao transporte do WebSockets. |

O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem. A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência. |

O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser usado para definir o cabeçalho de `Sec-WebSocket-Protocol` como um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções do cliente

As opções de cliente podem ser configuradas no tipo de `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript). Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` em si.

### <a name="configure-logging"></a>Configurar o registro em log

O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`. Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor. Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.

> [!NOTE]
> Para registrar provedores de log, você deve instalar os pacotes necessários. Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.

Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet. Chame o método de extensão `AddConsole`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, existe um método `configureLogging` semelhante. Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas. Os logs são gravados na janela do console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Em vez de um valor `LogLevel`, você também pode fornecer um valor de `string` que representa um nome de nível de log. Isso é útil ao configurar SignalR log em ambientes em que você não tem acesso às constantes `LogLevel`.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

A tabela a seguir lista os níveis de log disponíveis. O valor que você fornece para `configureLogging` define o nível de log **mínimo** que será registrado. As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.

| Cadeia de caracteres                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **ou** `information` | `LogLevel.Information` |
| `warn` **ou** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.

Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico deSignalR](xref:signalr/diagnostics).

O cliente Java SignalR usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log. É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica. O trecho de código a seguir mostra como usar `java.util.logging` com o cliente SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Os transportes usados pelo SignalR podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript). Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto de opções fornecido para `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Nesta versão do WebSocket do cliente Java é o único transporte disponível.

No cliente Java, o transporte é selecionado com o método `withTransport` no `HttpHubConnectionBuilder`. O cliente Java usa como padrão o transporte WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> O cliente Java SignalR ainda não dá suporte ao fallback de transporte.

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação junto com SignalR solicitações, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado. No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket). Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.

No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options no `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options no `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

No cliente Java SignalR, você pode configurar um token de portador para usar na autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava). Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar opções de tempo limite e Keep-Alive

Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |
| `HandshakeTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript). Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado. |

No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |
| `withHandshakeResponseTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado. |

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` em JavaScript) no `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS a serem enviados para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais a serem enviadas com cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | Somente WebSockets. A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento. Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP. Não é usado para conexões WebSocket. Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro. Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova instância de `HttpMessageHandler`. **Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP a ser usado ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets. Isso permite o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais do WebSocket. Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção de JavaScript | Valor Padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção Java | Valor Padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |
| `withHeader` `withHeaders` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP. |

---

No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`. O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`. A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto de `JsonSerializerSettings` de JSON.NET que pode ser usado para configurar a serialização de argumentos e valores de retorno. Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código no `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Não é possível configurar a serialização JSON no cliente JavaScript neste momento.

### <a name="messagepack-serialization-options"></a>Opções de serialização MessagePack

A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Consulte [MessagePack em SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve as opções para configurar os hubs de SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo. Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado. O valor recomendado é o dobro do valor de `KeepAliveInterval`.|
| `HandshakeTimeout` | 15 segundos | Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente. O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos com suporte neste Hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub. O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais. |

As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções de configuração avançada de HTTP

Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do ASP.NET Core SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o servidor armazena em buffer. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `AuthorizationData` | Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer. Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Consulte a seguir. | Opções adicionais específicas para o transporte de sondagem longa. |
| `WebSockets` | Consulte a seguir. | Opções adicionais específicas ao transporte do WebSockets. |

O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem. A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência. |

O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser usado para definir o cabeçalho de `Sec-WebSocket-Protocol` como um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções do cliente

As opções de cliente podem ser configuradas no tipo de `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript). Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` em si.

### <a name="configure-logging"></a>Configurar o registro em log

O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`. Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor. Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.

> [!NOTE]
> Para registrar provedores de log, você deve instalar os pacotes necessários. Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.

Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet. Chame o método de extensão `AddConsole`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, existe um método `configureLogging` semelhante. Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas. Os logs são gravados na janela do console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.

Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico deSignalR](xref:signalr/diagnostics).

O cliente Java SignalR usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log. É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica. O trecho de código a seguir mostra como usar `java.util.logging` com o cliente SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Os transportes usados pelo SignalR podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript). Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto de opções fornecido para `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Nesta versão do WebSocket do cliente Java é o único transporte disponível.

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação junto com SignalR solicitações, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado. No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket). Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.

No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options no `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options no `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

No cliente Java SignalR, você pode configurar um token de portador para usar na autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava). Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar opções de tempo limite e Keep-Alive

Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |
| `HandshakeTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript). Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado. |

No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |
| `withHandshakeResponseTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado. |

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` em JavaScript) no `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS a serem enviados para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais a serem enviadas com cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | Somente WebSockets. A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento. Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP. Não é usado para conexões WebSocket. Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro. Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova instância de `HttpMessageHandler`. **Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP a ser usado ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets. Isso permite o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais do WebSocket. Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção de JavaScript | Valor Padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção Java | Valor Padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |
| `withHeader` `withHeaders` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP. |

---

No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`. O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`. A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto de `JsonSerializerSettings` de JSON.NET que pode ser usado para configurar a serialização de argumentos e valores de retorno. Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código no `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Não é possível configurar a serialização JSON no cliente JavaScript neste momento.

### <a name="messagepack-serialization-options"></a>Opções de serialização MessagePack

A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Consulte [MessagePack em SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve as opções para configurar os hubs de SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 segundos | Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente. O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos com suporte neste Hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub. O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais. |

As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções de configuração avançada de HTTP

Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do ASP.NET Core SignalR:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o servidor armazena em buffer. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `AuthorizationData` | Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer. Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Consulte a seguir. | Opções adicionais específicas para o transporte de sondagem longa. |
| `WebSockets` | Consulte a seguir. | Opções adicionais específicas ao transporte do WebSockets. |

O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem. A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência. |

O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser usado para definir o cabeçalho de `Sec-WebSocket-Protocol` como um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções do cliente

As opções de cliente podem ser configuradas no tipo de `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript). Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` em si.

### <a name="configure-logging"></a>Configurar o registro em log

O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`. Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor. Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.

> [!NOTE]
> Para registrar provedores de log, você deve instalar os pacotes necessários. Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.

Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet. Chame o método de extensão `AddConsole`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, existe um método `configureLogging` semelhante. Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas. Os logs são gravados na janela do console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.

Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico deSignalR](xref:signalr/diagnostics).

O cliente Java SignalR usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log. É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica. O trecho de código a seguir mostra como usar `java.util.logging` com o cliente SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado com segurança.

### <a name="configure-allowed-transports"></a>Configurar transportes permitidos

Os transportes usados pelo SignalR podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript). Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto de opções fornecido para `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação junto com SignalR solicitações, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado. No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket). Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.

No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options no `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options no `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

No cliente Java SignalR, você pode configurar um token de portador para usar na autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava). Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar opções de tempo limite e Keep-Alive

Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |
| `HandshakeTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript). Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor, para dar tempo para que os pings cheguem. |
| `withHandshakeResponseTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` em JavaScript) no `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS a serem enviados para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais a serem enviadas com cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | Somente WebSockets. A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento. Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP. Não é usado para conexões WebSocket. Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro. Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova instância de `HttpMessageHandler`. **Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP a ser usado ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets. Isso permite o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais do WebSocket. Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção de JavaScript | Valor Padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção Java | Valor Padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure. |
| `withHeader` `withHeaders` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP. |

---

No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end