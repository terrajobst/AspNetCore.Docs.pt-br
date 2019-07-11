---
title: Configuração do ASP.NET SignalR Core
author: bradygaster
description: Saiba como configurar aplicativos do SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 8c9fcaecb04555718f5da6a42a8e56c258e795af
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813449"
---
# <a name="aspnet-core-signalr-configuration"></a>Configuração do ASP.NET SignalR Core

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

SignalR do ASP.NET Core dá suporte a dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

Serialização JSON pode ser configurada no servidor usando o [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) método de extensão, que pode ser adicionado após [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método. O `AddJsonProtocol` leva um delegado que recebe um `options` objeto. O [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) propriedade desse objeto é um JSON.NET `JsonSerializerSettings` objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno. Consulte a [documentação do JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes.

Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes de "camelCase" padrão, use o seguinte código:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

No cliente do .NET, o mesmo `AddJsonProtocol` método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:

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

MessagePack serialização pode ser configurada fornecendo um delegado para o [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) chamar. Ver [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização de MessagePack no cliente JavaScript neste momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve as opções de configuração hubs do SignalR:

::: moniker range=">= aspnetcore-3.0"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor irá considerar o cliente desconectado se ele ainda não recebeu uma mensagem (incluindo keep-alive) nesse intervalo. Ele pode demorar mais que esse intervalo de tempo limite para o cliente, na verdade, ser marcado como desconectado devido a como isso é implementado. O valor recomendado é double o `KeepAliveInterval` valor.|
| `HandshakeTimeout` | 15 segundos | Se o cliente não envia uma mensagem de handshake inicial dentro deste intervalo de tempo, a conexão será fechada. Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não enviou uma mensagem dentro deste intervalo, uma mensagem de ping é enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere o `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente. Recomendado `ServerTimeout` / `serverTimeoutInMilliseconds` valor é double o `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos os protocolos | Protocolos com suporte por esse hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas protocolos podem ser removidos dessa lista para desabilitar os protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de Hub. O padrão é `false`, conforme essas mensagens de exceção podem conter informações confidenciais. |
| `StreamBufferCapacity` | `10` | O número máximo de itens que podem ser armazenados em buffer para o cliente carrega fluxos. Se esse limite for atingido, o processamento de invocações é bloqueado até que o servidor processa itens de fluxo.|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor irá considerar o cliente desconectado se ele ainda não recebeu uma mensagem (incluindo keep-alive) nesse intervalo. Ele pode demorar mais que esse intervalo de tempo limite para o cliente, na verdade, ser marcado como desconectado devido a como isso é implementado. O valor recomendado é double o `KeepAliveInterval` valor.|
| `HandshakeTimeout` | 15 segundos | Se o cliente não envia uma mensagem de handshake inicial dentro deste intervalo de tempo, a conexão será fechada. Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não enviou uma mensagem dentro deste intervalo, uma mensagem de ping é enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere o `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente. Recomendado `ServerTimeout` / `serverTimeoutInMilliseconds` valor é double o `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos os protocolos | Protocolos com suporte por esse hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas protocolos podem ser removidos dessa lista para desabilitar os protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de Hub. O padrão é `false`, conforme essas mensagens de exceção podem conter informações confidenciais. |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 segundos | Se o cliente não envia uma mensagem de handshake inicial dentro deste intervalo de tempo, a conexão será fechada. Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não enviou uma mensagem dentro deste intervalo, uma mensagem de ping é enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere o `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente. Recomendado `ServerTimeout` / `serverTimeoutInMilliseconds` valor é double o `KeepAliveInterval` valor.  |
| `SupportedProtocols` | Todos os protocolos | Protocolos com suporte por esse hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas protocolos podem ser removidos dessa lista para desabilitar os protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de Hub. O padrão é `false`, conforme essas mensagens de exceção podem conter informações confidenciais. |

::: moniker-end

Opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para o `AddSignalR` chamar em `Startup.ConfigureServices`.

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

Opções para um único hub substituem as opções de globais fornecidas no `AddSignalR` e pode ser configurado usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções avançadas de configuração de HTTP

Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado para [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) em `Startup.Configure`.

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

A tabela a seguir descreve opções para configurar opções avançadas de HTTP do SignalR do ASP.NET Core:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que os buffers de servidor. Aumentar esse valor permite que o servidor receber mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `AuthorizationData` | Dados coletados automaticamente a partir de `Authorize` atributos aplicados à classe Hub. | Uma lista dos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos usados para determinar se um cliente está autorizado a conectar-se ao hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que os buffers de servidor. Aumentar esse valor permite que o servidor enviar mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Um bitmask de `HttpTransportType` valores que possam restringir os transportes de um cliente pode usar para se conectar. |
| `LongPolling` | Veja a seguir. | Opções adicionais específicas para o transporte de sondagem longa. |
| `WebSockets` | Veja a seguir. | Opções adicionais específicas para o transporte de WebSockets. |

O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando o `LongPolling` propriedade:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma solicitação de pesquisa único. Diminuir esse valor faz com que o cliente emitir novas solicitações de sondagem com mais frequência. |

O transporte de WebSocket tem opções adicionais que podem ser configuradas usando o `WebSockets` propriedade:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois de fecha o servidor, se o cliente não conseguir fechar dentro deste intervalo de tempo, a conexão é encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e deve retornar o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções do cliente

Opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .NET e JavaScript). Ele também está disponível no cliente de Java, mas o `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração construtor, bem como no `HubConnection` em si.

### <a name="configure-logging"></a>Configurar o registro em log

O log está configurado no cliente do .NET usando o `ConfigureLogging` método. Registro em log provedores e filtros pode ser registrado da mesma forma como estão no servidor. Consulte a [entrar no ASP.NET Core](xref:fundamentals/logging/index) documentação para obter mais informações.

> [!NOTE]
> Para registrar os provedores de log, você deve instalar os pacotes necessários. Consulte a [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) seção do docs para obter uma lista completa.

Por exemplo, para habilitar o log de Console, instale o `Microsoft.Extensions.Logging.Console` pacote do NuGet. Chamar o `AddConsole` método de extensão:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, um semelhante `configureLogging` existe um método. Forneça um `LogLevel` valor que indica o nível mínimo de mensagens de log para produzir. Os logs são gravados na janela de console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

Em vez de um `LogLevel` de valor, você também pode fornecer um `string` valor que representa um nome de nível de log. Isso é útil ao configurar o SignalR para registro em log em ambientes em que você não tem acesso para o `LogLevel` constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

A tabela a seguir lista os níveis de log disponíveis. O valor que você fornece para `configureLogging` define o **mínimo** nível será registrada de log. As mensagens registradas nesse nível, **ou os níveis listados depois na tabela**, serão registradas.

| cadeia de caracteres | LogLevel |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| `"info"` **Ou** `"information"` | `LogLevel.Information` |
| `"warn"` **Ou** `"warning"` | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> Para desabilitar o registro em log totalmente, especifique `signalR.LogLevel.None` no `configureLogging` método.

Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do SignalR](xref:signalr/diagnostics).

O cliente SignalR Java usa a [SLF4J](https://www.slf4j.org/) biblioteca para registro em log. É uma API de alto nível de log que permite aos usuários da biblioteca de escolher sua própria implementação de log específico, colocando em uma dependência de log específico. O trecho de código a seguir mostra como usar `java.util.logging` com o cliente de Java do SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se você não configurar o registro em log em suas dependências, SLF4J carrega um agente de operação não padrão com a seguinte mensagem de aviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Isso pode ser ignorado.

### <a name="configure-allowed-transports"></a>Configure transportes permitidos

Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamar (`withUrl` em JavaScript). Um bitwise OR dos valores de `HttpTransportType` pode ser usado para restringir o cliente para usar somente os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, para desabilitar o transporte de eventos do Server-Sent, mas permita conexões WebSocket e sondagem longa:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, transportes são configurados, definindo o `transport` campo no objeto de opções fornecido ao `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

Nesta versão do Java WebSocket do cliente é o transporte somente disponível.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

No cliente de Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder`. Os padrões de cliente Java para usar o transporte de WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> O cliente SignalR Java não dá suporte a fallback de transporte ainda.

::: moniker-end

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação junto com as solicitações de SignalR, use o `AccessTokenProvider` opção (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado. No cliente do .NET, esse token de acesso é passado como um HTTP "Autenticação do portador" token (usando o `Authorization` cabeçalho com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token de portador **exceto** em alguns casos em que o navegador APIs restringir a capacidade de aplicar cabeçalhos (especificamente, em solicitações de eventos do Server-sent e WebSockets). Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.

No cliente do .NET, o `AccessTokenProvider` opção pode ser especificada usando o delegado de opções no `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso é configurado definindo a `accessTokenFactory` campo no objeto de opções no `withUrl`:

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

No cliente de Java do SignalR, você pode configurar um token de portador para usar para autenticação, fornecendo uma fábrica de tokens de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [único\<cadeia de caracteres >](https://reactivex.io/documentation/single.html). Com uma chamada para [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever a lógica para gerar tokens de acesso do cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar tempo limite e opções de keep alive

Opções adicionais para configurar o tempo limite e o comportamento de keep alive estão disponíveis no `HubConnection` objeto em si:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente considera o servidor desconectado e gatilhos do `Closed` evento (`onclose` em JavaScript). Esse valor deve ser grande o suficiente para uma mensagem de ping a serem enviados do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número pelo menos duas vezes o servidor `KeepAliveInterval` valor, para dar tempo para pings de chegada. |
| `HandshakeTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancela o handshake e gatilhos do `Closed` evento (`onclose` em JavaScript). Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves. Para obter mais detalhes sobre o processo de Handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

No cliente do .NET, os valores de tempo limite são especificados como `TimeSpan` valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente considera as desconectado do servidor e os gatilhos de `onclose` eventos. Esse valor deve ser grande o suficiente para uma mensagem de ping a serem enviados do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número pelo menos duas vezes o servidor `KeepAliveInterval` valor, para dar tempo para pings de chegada. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ----------- | ------------- | ----------- |
|`getServerTimeout` `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não enviou uma mensagem nesse intervalo, o cliente considera as desconectado do servidor e os gatilhos de `onClose` eventos. Esse valor deve ser grande o suficiente para uma mensagem de ping a serem enviados do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número pelo menos duas vezes o servidor `KeepAliveInterval` valor, para dar tempo para pings de chegada. |
| `withHandshakeResponseTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancela o handshake e gatilhos de `onClose` eventos. Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves. Para obter mais detalhes sobre o processo de Handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem ser configuradas na `WithUrl` (`withUrl` em JavaScript) método em `HubConnectionBuilder` ou na configuração de várias APIs no `HttpHubConnectionBuilder` no cliente de Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção de .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Só tem suporte quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS para enviar para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP a ser enviado com cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais devem ser enviados com cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | WebSockets. A quantidade máxima de tempo que o cliente aguardará depois de fechamento para o servidor confirmar a solicitação de fechamento. Se o servidor não reconhece o fechamento dentro desse período, o cliente se desconecta. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com todas as solicitações HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP. Não é usado para conexões de WebSocket. Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro. Modificar as configurações nesse valor padrão e retorná-lo ou retornar um novo `HttpMessageHandler` instância. **Ao substituir o manipulador Certifique-se de copiar as configurações que você deseja impedir que o manipulador fornecido, caso contrário, as opções configuradas (como cabeçalhos e Cookies) não se aplicam ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP a ser usado ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina esse valor booliano para enviar as credenciais padrão para solicitações HTTP e WebSockets. Isso permite que o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais de WebSocket. Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usado para configurar as opções. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção de JavaScript | Valor padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Só tem suporte quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção de Java | Valor padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina isso como `true` para ignorar a etapa de negociação. **Só tem suporte quando o transporte de WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR. |
| `withHeader` `withHeaders` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com todas as solicitações HTTP. |

---

No cliente do .NET, essas opções podem ser modificadas pelo delegado opções fornecido para `WithUrl`:

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

No cliente de Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado das `HubConnectionBuilder.create("HUB URL")`

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
