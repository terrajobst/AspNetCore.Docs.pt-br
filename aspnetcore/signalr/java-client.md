---
title: Cliente de Java do SignalR Core ASP.NET
author: mikaelm12
description: Saiba como usar o cliente de Java de SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 03/14/2019
uid: signalr/java-client
ms.openlocfilehash: 09e5ce23ddcc250d212a8cdf1176f39531a9c0ba
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978482"
---
# <a name="aspnet-core-signalr-java-client"></a>Cliente de Java do SignalR Core ASP.NET

Por [Mikael Mengistu](https://twitter.com/MikaelM_12)

O cliente de Java permite se conectar a um servidor do SignalR do ASP.NET Core do código Java, incluindo aplicativos Android. Como o [cliente JavaScript](xref:signalr/javascript-client) e o [cliente .NET](xref:signalr/dotnet-client), o cliente de Java permite que você receber e enviar mensagens a um hub em tempo real. O cliente de Java está disponível no ASP.NET Core 2.2 e posterior.

O aplicativo de console do Java de exemplo referenciado neste artigo usa o cliente de Java do SignalR.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-java-client-package"></a>Instalar o pacote de cliente Java do SignalR

O *1.0.0 signalr* arquivo JAR permite que os clientes se conectem aos hubs de SignalR. Para localizar o número de versão de arquivo JAR mais recente, consulte o [resultados da pesquisa Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Se usando o Gradle, adicione a seguinte linha para o `dependencies` seção do seu *Build. gradle* arquivo:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Se usando o Maven, adicione as seguintes linhas dentro de `<dependencies>` elemento da sua *POM. XML* arquivo:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Conectar a um hub

Para estabelecer uma `HubConnection`, o `HubConnectionBuilder` deve ser usado. O nível de log e a URL do hub pode ser configurado durante a criação de uma conexão. Configurar as opções necessárias chamando o `HubConnectionBuilder` métodos antes `build`. Iniciar a conexão com `start`.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Chamar métodos de hub do cliente

Uma chamada para `send` invoca um método de hub. Passe o nome do método de hub e quaisquer argumentos definidos no método de hub para `send`.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Se você estiver usando o serviço do Azure SignalR no *modo sem servidor*, você não pode chamar métodos de hub de um cliente. Para obter mais informações, consulte o [documentação do SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do hub

Use `hubConnection.on` para definir métodos no cliente que o hub pode chamar. Defina os métodos depois de criar, mas antes de iniciar a conexão.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Adicionar registro em log

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

## <a name="android-development-notes"></a>Notas de desenvolvimento do Android

Com relação à compatibilidade do SDK do Android para os recursos de cliente do SignalR, considere os seguintes itens ao especificar sua versão do SDK do Android de destino:

* O cliente de Java do SignalR será executado no nível da API Android 16 e versões posteriores.
* Conectar-se por meio do serviço Azure SignalR exigirá o nível da API Android 20 e posterior porque a [serviço do Azure SignalR](/azure/azure-signalr/signalr-overview) requer o TLS 1.2 e não dá suporte a conjuntos de codificação baseado em SHA-1. Android [adicionou suporte para SHA-256 (e superior) conjuntos de codificação](https://developer.android.com/reference/javax/net/ssl/SSLSocket) no nível da API 20.

## <a name="configure-bearer-token-authentication"></a>Configurar a autenticação de token de portador

No cliente de Java do SignalR, você pode configurar um token de portador para usar para autenticação, fornecendo uma "access token fábrica" para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [único<String>](http://reactivex.io/documentation/single.html). Com uma chamada para [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever a lógica para gerar tokens de acesso do cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Limitações conhecidas

* Há suporte para apenas o protocolo JSON.
* Há suporte para apenas o transporte de WebSockets.
* Streaming ainda não tem suporte.

## <a name="additional-resources"></a>Recursos adicionais

* [Referência de API Java](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Documentação de sem servidor SignalR Service do Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
