---
title: Redis backplane para ASP.NET Core SignalR escalar horizontalmente
author: bradygaster
description: Saiba como configurar um backplane Redis para habilitar a expansão para um aplicativo ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 0461fc6a212ba78111bc2054cca74951721c5820
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289033"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a>Configurar um Redis backplane para ASP.NET Core SignalR escalar horizontalmente

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse), [Brady GASTER](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),

Este artigo explica SignalRaspectos específicos da configuração de um servidor [Redis](https://redis.io/) a ser usado para escalar horizontalmente um ASP.NET Core SignalR aplicativo.

## <a name="set-up-a-redis-backplane"></a>Configurar um backplane Redis

* Implantar um servidor Redis.

  > [!IMPORTANT] 
  > Para uso em produção, um backplane Redis é recomendado somente quando executado na mesma data center que o aplicativo SignalR. Caso contrário, a latência de rede degrada o desempenho. Se seu aplicativo SignalR estiver em execução na nuvem do Azure, recomendamos o serviço de SignalR do Azure em vez de um backplane Redis. Você pode usar o serviço de cache Redis do Azure para ambientes de desenvolvimento e teste.

  Para obter mais informações, consulte os seguintes recursos:

  * <xref:signalr/scale>
  * [Documentação do Redis](https://redis.io/)
  * [Documentação do cache Redis do Azure](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* No aplicativo SignalR, instale o pacote NuGet do `Microsoft.AspNetCore.SignalR.Redis`.
* No método `Startup.ConfigureServices`, chame `AddRedis` após `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Configure as opções conforme necessário:
 
  A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . As opções especificadas em `ConfigurationOptions` substituem aquelas definidas na cadeia de conexão.

  O exemplo a seguir mostra como definir opções no objeto `ConfigurationOptions`. Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  No código anterior, `options.Configuration` é inicializado com o que for especificado na cadeia de conexão.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* No aplicativo SignalR, instale um dos seguintes pacotes NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`-depende de StackExchange. Redis 2. X.X. Este é o pacote recomendado para o ASP.NET Core 2,2 e posterior.
  * `Microsoft.AspNetCore.SignalR.Redis`-depende de StackExchange. Redis 1. X.X. Este pacote não está incluído no ASP.NET Core 3,0 e posterior.

* No método `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Ao usar `Microsoft.AspNetCore.SignalR.Redis`, chame <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

* Configure as opções conforme necessário:
 
  A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . As opções especificadas em `ConfigurationOptions` substituem aquelas definidas na cadeia de conexão.

  O exemplo a seguir mostra como definir opções no objeto `ConfigurationOptions`. Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Ao usar `Microsoft.AspNetCore.SignalR.Redis`, chame <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

  No código anterior, `options.Configuration` é inicializado com o que for especificado na cadeia de conexão.

  Para obter informações sobre as opções de Redis, consulte a [documentação do StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* No aplicativo SignalR, instale o seguinte pacote NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* No método `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Configure as opções conforme necessário:
 
  A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . As opções especificadas em `ConfigurationOptions` substituem aquelas definidas na cadeia de conexão.

  O exemplo a seguir mostra como definir opções no objeto `ConfigurationOptions`. Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  No código anterior, `options.Configuration` é inicializado com o que for especificado na cadeia de conexão.

  Para obter informações sobre as opções de Redis, consulte a [documentação do StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Se você estiver usando um servidor Redis para vários aplicativos SignalR, use um prefixo de canal diferente para cada aplicativo SignalR.

  Definir um prefixo de canal isola um aplicativo SignalR de outros que usam prefixos de canal diferentes. Se você não atribuir prefixos diferentes, uma mensagem enviada de um aplicativo para todos os seus próprios clientes acessará todos os clientes de todos os aplicativos que usam o servidor Redis como um backplane.

* Configure o software de balanceamento de carga do farm de servidores para sessões adesivas. Aqui estão alguns exemplos de documentação sobre como fazer isso:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Erros do servidor Redis

Quando um servidor Redis fica inativo, SignalR gera exceções que indicam que as mensagens não serão entregues. Algumas mensagens de exceção típicas:

* *Falha ao gravar mensagem*
* *Falha ao invocar o método de Hub ' MethodName '*
* *Falha na conexão com o Redis*

SignalR não armazena em buffer as mensagens para enviá-las quando o servidor volta a ficar em funcionamento. Todas as mensagens enviadas enquanto o servidor Redis está inoperante são perdidas.

SignalR se reconecta automaticamente quando o servidor Redis está disponível novamente.

### <a name="custom-behavior-for-connection-failures"></a>Comportamento personalizado para falhas de conexão

Aqui está um exemplo que mostra como lidar com eventos de falha de conexão do Redis.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Clustering Redis

O [clustering Redis](https://redis.io/topics/cluster-spec) é um método para obter alta disponibilidade usando vários servidores Redis. O clustering não tem suporte oficialmente, mas pode funcionar.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* <xref:signalr/scale>
* [Documentação do Redis](https://redis.io/documentation)
* [Documentação do StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Documentação do cache Redis do Azure](https://docs.microsoft.com/azure/redis-cache/)
