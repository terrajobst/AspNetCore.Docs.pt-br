---
title: Redis backplane de expansão do SignalR do ASP.NET Core
author: bradygaster
description: Saiba como configurar um backplane de Redis para habilitar a escala horizontal para um aplicativo do SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: adf9bbce1353fd811a4044e173533f76bc4193de
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152919"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>Configurar um backplane de Redis para expansão do SignalR do ASP.NET Core

Por [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), e [Tom Dykstra](https://github.com/tdykstra),

Este artigo explica os aspectos de SignalR específicas de configuração de um [Redis](https://redis.io/) servidor a ser usado para dimensionar um aplicativo do SignalR do ASP.NET Core.

## <a name="set-up-a-redis-backplane"></a>Configurar um backplane de Redis

* Implante um servidor do Redis.

  > [!IMPORTANT] 
  > Para uso em produção, um backplane de Redis é recomendável somente quando ele é executado no mesmo data center que o aplicativo do SignalR. Caso contrário, latência de rede degrada o desempenho. Se seu aplicativo SignalR está em execução na nuvem do Azure, é recomendável o serviço do Azure SignalR em vez de um backplane de Redis. Você pode usar o serviço de Cache Redis do Azure para desenvolvimento e ambientes de teste.

  Para obter mais informações, consulte os seguintes recursos:

  * <xref:signalr/scale>
  * [Documentação do redis](https://redis.io/)
  * [Documentação do Cache Redis do Azure](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* No aplicativo do SignalR, instale o `Microsoft.AspNetCore.SignalR.Redis` pacote do NuGet. (Há também um `Microsoft.AspNetCore.SignalR.StackExchangeRedis` empacotar, mas que um é para o ASP.NET Core 2.2 e posterior.)

* No `Startup.ConfigureServices` método, chame `AddRedis` depois `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Defina as opções conforme necessário:
 
  A maioria das opções pode ser definido na cadeia de conexão ou nos [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objeto. As opções especificadas em `ConfigurationOptions` substituirão as definido na cadeia de conexão.

  O exemplo a seguir mostra como definir opções no `ConfigurationOptions` objeto. Este exemplo adiciona um prefixo de canal para que vários aplicativos podem compartilhar a mesma instância do Redis, conforme explicado na etapa a seguir.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  No código anterior, `options.Configuration` é inicializada com tudo o que foi especificado na cadeia de conexão.

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* No aplicativo do SignalR, instale um dos seguintes pacotes NuGet:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -Depende do stackexchange. Redis 2.X.X. Este é o pacote recomendado para o ASP.NET Core 2.2 e posterior.
  * `Microsoft.AspNetCore.SignalR.Redis` -Depende do 1.X.X stackexchange. Redis. Este pacote não enviará no ASP.NET Core 3.0.

* No `Startup.ConfigureServices` método, chame `AddStackExchangeRedis` depois `AddSignalR`:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* Defina as opções conforme necessário:
 
  A maioria das opções pode ser definido na cadeia de conexão ou nos [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objeto. As opções especificadas em `ConfigurationOptions` substituirão as definido na cadeia de conexão.

  O exemplo a seguir mostra como definir opções no `ConfigurationOptions` objeto. Este exemplo adiciona um prefixo de canal para que vários aplicativos podem compartilhar a mesma instância do Redis, conforme explicado na etapa a seguir.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  No código anterior, `options.Configuration` é inicializada com tudo o que foi especificado na cadeia de conexão.

  Para obter informações sobre as opções do Redis, consulte o [StackExchange Redis documentação](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Se você estiver usando um servidor de Redis para vários aplicativos do SignalR, use um prefixo de canal diferente para cada aplicativo do SignalR.

  Configurar um prefixo de canal isola a um aplicativo do SignalR de outras pessoas que usam os prefixos de canal diferente. Se você não atribuir prefixos diferentes, uma mensagem enviada de um aplicativo para todos os seus próprios clientes irão para todos os clientes de todos os aplicativos que usam o servidor Redis como um backplane.

* Configure seu servidor farm balanceamento de carga para sessões adesivas. Aqui estão alguns exemplos de documentação sobre como fazer isso:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Erros do servidor de redis

Quando um servidor Redis fica inativo, o SignalR gera exceções que indicam as mensagens não entregues. Algumas mensagens de exceção típico:

* *Mensagem de gravação com falha*
* *Falha ao invocar o método de hub 'MethodName'*
* *Falha na Conexão ao Redis*

O SignalR não armazena em buffer as mensagens para enviá-las quando o servidor de volta a funcionar. Todas as mensagens enviadas enquanto o servidor Redis estiver inativo serão perdidas.

O SignalR se reconecta automaticamente quando o servidor Redis estiver disponível novamente.

### <a name="custom-behavior-for-connection-failures"></a>Comportamento personalizado para falhas de conexão

Aqui está um exemplo que mostra como manipular eventos de falha de conexão do Redis.

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

## <a name="redis-clustering"></a>Clustering do redis

[Clustering de redis](https://redis.io/topics/cluster-spec) é um método para alcançar alta disponibilidade por meio de vários servidores do Redis. Clustering não é oficialmente suportado, mas pode funcionar.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* <xref:signalr/scale>
* [Documentação do redis](https://redis.io/documentation)
* [Documentação do StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Documentação do Cache Redis do Azure](https://docs.microsoft.com/azure/redis-cache/)
