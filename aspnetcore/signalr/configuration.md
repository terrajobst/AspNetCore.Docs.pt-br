---
title: Configuração do Signalr ASP.NET Core
author: bradygaster
description: Saiba como configurar ASP.NET Core aplicativos Signalr.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 156ffac83fbdf61fd88ad8acc307c2c701c46bca
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773924"
---
# <a name="aspnet-core-signalr-configuration"></a>Configuração do Signalr ASP.NET Core

## <a name="jsonmessagepack-serialization-options"></a>Opções de serialização JSON/MessagePack

ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Cada protocolo tem opções de configuração de serialização.

A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`. O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto. A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno. Consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes.

Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

No cliente .net, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:

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

A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.

> [!NOTE]
> Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.

## <a name="configure-server-options"></a>Configurar opções de servidor

A tabela a seguir descreve as opções para configurar os hubs de sinalização:

::: moniker range=">= aspnetcore-3.0"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo. Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado. O valor recomendado é o dobro `KeepAliveInterval` do valor.|
| `HandshakeTimeout` | 15 segundos | Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` . O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos com suporte neste Hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub. O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais. |
| `StreamBufferCapacity` | `10` | O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente. Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.|
| `MaximumReceiveMessageSize` | 32 KB | Tamanho máximo de uma única mensagem de Hub de entrada. |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo. Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado. O valor recomendado é o dobro `KeepAliveInterval` do valor.|
| `HandshakeTimeout` | 15 segundos | Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` . O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos com suporte neste Hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub. O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais. |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 segundos | Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta. Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` . O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Todos os protocolos instalados | Protocolos com suporte neste Hub. Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub. O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais. |

::: moniker-end

As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.

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

As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opções de configuração avançada de HTTP

::: moniker range=">= aspnetcore-3.0"

Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado [para\<MapHub T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória. Essas opções são configuradas passando um delegado [para\<MapHub T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no. `Startup.Configure`

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

::: moniker-end

A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:

::: moniker range=">= aspnetcore-3.0"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda. Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória. |
| `AuthorizationData` | Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back. Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Veja abaixo. | Opções adicionais específicas para o transporte de sondagem longa. |
| `WebSockets` | Veja abaixo. | Opções adicionais específicas ao transporte do WebSockets. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | O número máximo de bytes recebidos do cliente que o servidor armazena em buffer. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `AuthorizationData` | Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub. | Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub. |
| `TransportMaxBufferSize` | 32 KB | O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer. Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `Transports` | Todos os transportes estão habilitados. | Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar. |
| `LongPolling` | Veja abaixo. | Opções adicionais específicas para o transporte de sondagem longa. |
| `WebSockets` | Veja abaixo. | Opções adicionais específicas ao transporte do WebSockets. |

::: moniker-end

O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem. A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência. |

O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada. |
| `SubProtocolSelector` | `null` | Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado. O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado. |

## <a name="configure-client-options"></a>Configurar opções do cliente

As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript). Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como `HubConnection` por si só.

### <a name="configure-logging"></a>Configurar registro em log

O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método. Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor. Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.

> [!NOTE]
> Para registrar provedores de log, você deve instalar os pacotes necessários. Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.

Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet. Chame o `AddConsole` método de extensão:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

No cliente JavaScript, existe um método `configureLogging` semelhante. Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas. Os logs são gravados na janela do console do navegador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log. Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

A tabela a seguir lista os níveis de log disponíveis. O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado. As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.

| Cadeia de Caracteres                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**ou**`information` | `LogLevel.Information` |
| `warn`**ou**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> Para desabilitar completamente o registro `signalR.LogLevel.None` `configureLogging` em log, especifique no método.

Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).

O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log. É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica. O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.

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

Os transportes usados pelo signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript). Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados. Todos os transportes são habilitados por padrão.

Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

Nesta versão do WebSocket do cliente Java é o único transporte disponível.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

No cliente Java, o transporte é selecionado com o `withTransport` método `HttpHubConnectionBuilder`no. O cliente Java usa como padrão o transporte WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.

::: moniker-end

### <a name="configure-bearer-authentication"></a>Configurar a autenticação do portador

Para fornecer dados de autenticação juntamente com solicitações do signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado. No cliente .net, esse token de acesso é passado como um token http "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`). No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket). Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.

No cliente .net, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em: `WithUrl`

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`

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

No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava). Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar opções de tempo limite e Keep-Alive

Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem. |
| `HandshakeTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript). Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado. |

No cliente .net, os valores de tempo limite são `TimeSpan` especificados como valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem. |
| `withHandshakeResponseTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milissegundos) | Determina o intervalo no qual o cliente envia mensagens de ping. O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo. Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado. |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript). Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem. |
| `HandshakeTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript). Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

No cliente .net, os valores de tempo limite são `TimeSpan` especificados como valores.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milissegundos) | Tempo limite para a atividade do servidor. Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento. Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite. O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor, para dar tempo para que os pings cheguem. |
| `withHandshakeResponseTimeout` | 15 segundos | Tempo limite para o handshake inicial do servidor. Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento. Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave. Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

::: moniker-end

---

### <a name="configure-additional-options"></a>Configurar opções adicionais

Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em `HttpHubConnectionBuilder` ou em várias APIs de configuração no cliente Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Opção .NET |  Valor padrão | Descrição |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `SkipNegotiation` | `false` | Defina como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure. |
| `ClientCertificates` | Vazio | Uma coleção de certificados TLS a serem enviados para autenticar solicitações. |
| `Cookies` | Vazio | Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP. |
| `Credentials` | Vazio | Credenciais a serem enviadas com cada solicitação HTTP. |
| `CloseTimeout` | 5 segundos | Somente WebSockets. A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento. Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará. |
| `Headers` | Vazio | Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP. |
| `HttpMessageHandlerFactory` | `null` | Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP. Não é usado para conexões WebSocket. Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro. Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância. **Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.** |
| `Proxy` | `null` | Um proxy HTTP a ser usado ao enviar solicitações HTTP. |
| `UseDefaultCredentials` | `false` | Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets. Isso permite o uso da autenticação do Windows. |
| `WebSocketConfiguration` | `null` | Um delegado que pode ser usado para configurar opções adicionais do WebSocket. Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Opção de JavaScript | Valor padrão | Descrição |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `skipNegotiation` | `false` | Defina como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Opção Java | Valor padrão | Descrição |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP. |
| `shouldSkipNegotiate` | `false` | Defina como `true` para ignorar a etapa de negociação. **Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**. Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure. |
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

No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`

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
