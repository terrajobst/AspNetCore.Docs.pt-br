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
ms.openlocfilehash: 379d46fcaabb8eb0d04e521a5ad698229f947b7c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963912"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a><span data-ttu-id="33ad9-103">Configurar um Redis backplane para ASP.NET Core SignalR escalar horizontalmente</span><span class="sxs-lookup"><span data-stu-id="33ad9-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="33ad9-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse), [Brady GASTER](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="33ad9-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="33ad9-105">Este artigo explica SignalRaspectos específicos da configuração de um servidor [Redis](https://redis.io/) a ser usado para escalar horizontalmente um ASP.NET Core SignalR aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33ad9-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="33ad9-106">Configurar um backplane Redis</span><span class="sxs-lookup"><span data-stu-id="33ad9-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="33ad9-107">Implantar um servidor Redis.</span><span class="sxs-lookup"><span data-stu-id="33ad9-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="33ad9-108">Para uso em produção, um backplane Redis é recomendado somente quando executado na mesma data center que o aplicativo SignalR.</span><span class="sxs-lookup"><span data-stu-id="33ad9-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="33ad9-109">Caso contrário, a latência de rede degrada o desempenho.</span><span class="sxs-lookup"><span data-stu-id="33ad9-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="33ad9-110">Se seu aplicativo SignalR estiver em execução na nuvem do Azure, recomendamos o serviço de SignalR do Azure em vez de um backplane Redis.</span><span class="sxs-lookup"><span data-stu-id="33ad9-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="33ad9-111">Você pode usar o serviço de cache Redis do Azure para ambientes de desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="33ad9-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="33ad9-112">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="33ad9-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="33ad9-113">Documentação do Redis</span><span class="sxs-lookup"><span data-stu-id="33ad9-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="33ad9-114">Documentação do cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="33ad9-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="33ad9-115">No aplicativo SignalR, instale o pacote NuGet do `Microsoft.AspNetCore.SignalR.Redis`.</span><span class="sxs-lookup"><span data-stu-id="33ad9-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="33ad9-116">(Há também um pacote `Microsoft.AspNetCore.SignalR.StackExchangeRedis`, mas esse é para o ASP.NET Core 2,2 e posterior.)</span><span class="sxs-lookup"><span data-stu-id="33ad9-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="33ad9-117">No método `Startup.ConfigureServices`, chame `AddRedis` após `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="33ad9-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="33ad9-118">Configure as opções conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="33ad9-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="33ad9-119">A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="33ad9-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="33ad9-120">As opções especificadas em `ConfigurationOptions` substituem aquelas definidas na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="33ad9-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="33ad9-121">O exemplo a seguir mostra como definir opções no objeto `ConfigurationOptions`.</span><span class="sxs-lookup"><span data-stu-id="33ad9-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="33ad9-122">Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.</span><span class="sxs-lookup"><span data-stu-id="33ad9-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="33ad9-123">No código anterior, `options.Configuration` é inicializado com o que for especificado na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="33ad9-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="33ad9-124">No aplicativo SignalR, instale um dos seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="33ad9-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="33ad9-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-depende de StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="33ad9-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="33ad9-126">Este é o pacote recomendado para o ASP.NET Core 2,2 e posterior.</span><span class="sxs-lookup"><span data-stu-id="33ad9-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="33ad9-127">`Microsoft.AspNetCore.SignalR.Redis`-depende de StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="33ad9-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="33ad9-128">Este pacote não será enviado no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="33ad9-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="33ad9-129">No método `Startup.ConfigureServices`, chame `AddStackExchangeRedis` após `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="33ad9-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="33ad9-130">Configure as opções conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="33ad9-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="33ad9-131">A maioria das opções pode ser definida na cadeia de conexão ou no objeto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="33ad9-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="33ad9-132">As opções especificadas em `ConfigurationOptions` substituem aquelas definidas na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="33ad9-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="33ad9-133">O exemplo a seguir mostra como definir opções no objeto `ConfigurationOptions`.</span><span class="sxs-lookup"><span data-stu-id="33ad9-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="33ad9-134">Este exemplo adiciona um prefixo de canal para que vários aplicativos possam compartilhar a mesma instância de Redis, conforme explicado na etapa a seguir.</span><span class="sxs-lookup"><span data-stu-id="33ad9-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="33ad9-135">No código anterior, `options.Configuration` é inicializado com o que for especificado na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="33ad9-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="33ad9-136">Para obter informações sobre as opções de Redis, consulte a [documentação do StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="33ad9-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="33ad9-137">Se você estiver usando um servidor Redis para vários aplicativos SignalR, use um prefixo de canal diferente para cada aplicativo SignalR.</span><span class="sxs-lookup"><span data-stu-id="33ad9-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="33ad9-138">Definir um prefixo de canal isola um aplicativo SignalR de outros que usam prefixos de canal diferentes.</span><span class="sxs-lookup"><span data-stu-id="33ad9-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="33ad9-139">Se você não atribuir prefixos diferentes, uma mensagem enviada de um aplicativo para todos os seus próprios clientes acessará todos os clientes de todos os aplicativos que usam o servidor Redis como um backplane.</span><span class="sxs-lookup"><span data-stu-id="33ad9-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="33ad9-140">Configure o software de balanceamento de carga do farm de servidores para sessões adesivas.</span><span class="sxs-lookup"><span data-stu-id="33ad9-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="33ad9-141">Aqui estão alguns exemplos de documentação sobre como fazer isso:</span><span class="sxs-lookup"><span data-stu-id="33ad9-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="33ad9-142">IIS</span><span class="sxs-lookup"><span data-stu-id="33ad9-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="33ad9-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="33ad9-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="33ad9-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="33ad9-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="33ad9-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="33ad9-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="33ad9-146">Erros do servidor Redis</span><span class="sxs-lookup"><span data-stu-id="33ad9-146">Redis server errors</span></span>

<span data-ttu-id="33ad9-147">Quando um servidor Redis fica inativo, SignalR gera exceções que indicam que as mensagens não serão entregues.</span><span class="sxs-lookup"><span data-stu-id="33ad9-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="33ad9-148">Algumas mensagens de exceção típicas:</span><span class="sxs-lookup"><span data-stu-id="33ad9-148">Some typical exception messages:</span></span>

* <span data-ttu-id="33ad9-149">*Falha ao gravar mensagem*</span><span class="sxs-lookup"><span data-stu-id="33ad9-149">*Failed writing message*</span></span>
* <span data-ttu-id="33ad9-150">*Falha ao invocar o método de Hub ' MethodName '*</span><span class="sxs-lookup"><span data-stu-id="33ad9-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="33ad9-151">*Falha na conexão com o Redis*</span><span class="sxs-lookup"><span data-stu-id="33ad9-151">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="33ad9-152"> não armazena em buffer as mensagens para enviá-las quando o servidor volta a ficar em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="33ad9-152"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="33ad9-153">Todas as mensagens enviadas enquanto o servidor Redis está inoperante são perdidas.</span><span class="sxs-lookup"><span data-stu-id="33ad9-153">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="33ad9-154"> se reconecta automaticamente quando o servidor Redis está disponível novamente.</span><span class="sxs-lookup"><span data-stu-id="33ad9-154"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="33ad9-155">Comportamento personalizado para falhas de conexão</span><span class="sxs-lookup"><span data-stu-id="33ad9-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="33ad9-156">Aqui está um exemplo que mostra como lidar com eventos de falha de conexão do Redis.</span><span class="sxs-lookup"><span data-stu-id="33ad9-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="33ad9-157">Clustering Redis</span><span class="sxs-lookup"><span data-stu-id="33ad9-157">Redis Clustering</span></span>

<span data-ttu-id="33ad9-158">O [clustering Redis](https://redis.io/topics/cluster-spec) é um método para obter alta disponibilidade usando vários servidores Redis.</span><span class="sxs-lookup"><span data-stu-id="33ad9-158">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="33ad9-159">O clustering não tem suporte oficialmente, mas pode funcionar.</span><span class="sxs-lookup"><span data-stu-id="33ad9-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="33ad9-160">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="33ad9-160">Next steps</span></span>

<span data-ttu-id="33ad9-161">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="33ad9-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="33ad9-162">Documentação do Redis</span><span class="sxs-lookup"><span data-stu-id="33ad9-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="33ad9-163">Documentação do StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="33ad9-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="33ad9-164">Documentação do cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="33ad9-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
