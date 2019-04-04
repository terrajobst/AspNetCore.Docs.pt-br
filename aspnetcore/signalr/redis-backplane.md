---
title: Redis backplane de expansão do SignalR do ASP.NET Core
author: bradygaster
description: Saiba como configurar um backplane de Redis para habilitar a escala horizontal para um aplicativo do SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: 9d2a942dba6abe669126efee7f2b3cdd6560658e
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012637"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="c9280-103">Configurar um backplane de Redis para expansão do SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9280-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="c9280-104">Por [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), e [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="c9280-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="c9280-105">Este artigo explica os aspectos de SignalR específicas de configuração de um [Redis](https://redis.io/) servidor a ser usado para dimensionar um aplicativo do SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c9280-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="c9280-106">Configurar um backplane de Redis</span><span class="sxs-lookup"><span data-stu-id="c9280-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="c9280-107">Implante um servidor do Redis.</span><span class="sxs-lookup"><span data-stu-id="c9280-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="c9280-108">Para uso em produção, um backplane de Redis é recomendável somente quando ele é executado no mesmo data center que o aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c9280-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="c9280-109">Caso contrário, latência de rede degrada o desempenho.</span><span class="sxs-lookup"><span data-stu-id="c9280-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="c9280-110">Se seu aplicativo SignalR está em execução na nuvem do Azure, é recomendável o serviço do Azure SignalR em vez de um backplane de Redis.</span><span class="sxs-lookup"><span data-stu-id="c9280-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="c9280-111">Você pode usar o serviço de Cache Redis do Azure para desenvolvimento e ambientes de teste.</span><span class="sxs-lookup"><span data-stu-id="c9280-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="c9280-112">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="c9280-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="c9280-113">Documentação do redis</span><span class="sxs-lookup"><span data-stu-id="c9280-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="c9280-114">Documentação do Cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="c9280-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="c9280-115">No aplicativo do SignalR, instale o `Microsoft.AspNetCore.SignalR.Redis` pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="c9280-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="c9280-116">(Há também um `Microsoft.AspNetCore.SignalR.StackExchangeRedis` empacotar, mas que um é para o ASP.NET Core 2.2 e posterior.)</span><span class="sxs-lookup"><span data-stu-id="c9280-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="c9280-117">No `Startup.ConfigureServices` método, chame `AddRedis` depois `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="c9280-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="c9280-118">Defina as opções conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="c9280-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="c9280-119">A maioria das opções pode ser definido na cadeia de conexão ou nos [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objeto.</span><span class="sxs-lookup"><span data-stu-id="c9280-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c9280-120">As opções especificadas em `ConfigurationOptions` substituirão as definido na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="c9280-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c9280-121">O exemplo a seguir mostra como definir opções no `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="c9280-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c9280-122">Este exemplo adiciona um prefixo de canal para que vários aplicativos podem compartilhar a mesma instância do Redis, conforme explicado na etapa a seguir.</span><span class="sxs-lookup"><span data-stu-id="c9280-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="c9280-123">No código anterior, `options.Configuration` é inicializada com tudo o que foi especificado na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="c9280-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="c9280-124">No aplicativo do SignalR, instale um dos seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="c9280-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` <span data-ttu-id="c9280-125">-Depende do stackexchange. Redis 2.X.X.</span><span class="sxs-lookup"><span data-stu-id="c9280-125">- Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="c9280-126">Este é o pacote recomendado para o ASP.NET Core 2.2 e posterior.</span><span class="sxs-lookup"><span data-stu-id="c9280-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * `Microsoft.AspNetCore.SignalR.Redis` <span data-ttu-id="c9280-127">-Depende do 1.X.X stackexchange. Redis.</span><span class="sxs-lookup"><span data-stu-id="c9280-127">- Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="c9280-128">Este pacote não enviará no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="c9280-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="c9280-129">No `Startup.ConfigureServices` método, chame `AddStackExchangeRedis` depois `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="c9280-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="c9280-130">Defina as opções conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="c9280-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="c9280-131">A maioria das opções pode ser definido na cadeia de conexão ou nos [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) objeto.</span><span class="sxs-lookup"><span data-stu-id="c9280-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c9280-132">As opções especificadas em `ConfigurationOptions` substituirão as definido na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="c9280-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c9280-133">O exemplo a seguir mostra como definir opções no `ConfigurationOptions` objeto.</span><span class="sxs-lookup"><span data-stu-id="c9280-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c9280-134">Este exemplo adiciona um prefixo de canal para que vários aplicativos podem compartilhar a mesma instância do Redis, conforme explicado na etapa a seguir.</span><span class="sxs-lookup"><span data-stu-id="c9280-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="c9280-135">No código anterior, `options.Configuration` é inicializada com tudo o que foi especificado na cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="c9280-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="c9280-136">Para obter informações sobre as opções do Redis, consulte o [StackExchange Redis documentação](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="c9280-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="c9280-137">Se você estiver usando um servidor de Redis para vários aplicativos do SignalR, use um prefixo de canal diferente para cada aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="c9280-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="c9280-138">Configurar um prefixo de canal isola a um aplicativo do SignalR de outras pessoas que usam os prefixos de canal diferente.</span><span class="sxs-lookup"><span data-stu-id="c9280-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="c9280-139">Se você não atribuir prefixos diferentes, uma mensagem enviada de um aplicativo para todos os seus próprios clientes irão para todos os clientes de todos os aplicativos que usam o servidor Redis como um backplane.</span><span class="sxs-lookup"><span data-stu-id="c9280-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="c9280-140">Configure seu servidor farm balanceamento de carga para sessões adesivas.</span><span class="sxs-lookup"><span data-stu-id="c9280-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="c9280-141">Aqui estão alguns exemplos de documentação sobre como fazer isso:</span><span class="sxs-lookup"><span data-stu-id="c9280-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="c9280-142">IIS</span><span class="sxs-lookup"><span data-stu-id="c9280-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="c9280-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="c9280-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="c9280-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="c9280-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="c9280-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="c9280-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="c9280-146">Erros do servidor de redis</span><span class="sxs-lookup"><span data-stu-id="c9280-146">Redis server errors</span></span>

<span data-ttu-id="c9280-147">Quando um servidor Redis fica inativo, o SignalR gera exceções que indicam as mensagens não entregues.</span><span class="sxs-lookup"><span data-stu-id="c9280-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="c9280-148">Algumas mensagens de exceção típico:</span><span class="sxs-lookup"><span data-stu-id="c9280-148">Some typical exception messages:</span></span>

* *<span data-ttu-id="c9280-149">Mensagem de gravação com falha</span><span class="sxs-lookup"><span data-stu-id="c9280-149">Failed writing message</span></span>*
* *<span data-ttu-id="c9280-150">Falha ao invocar o método de hub 'MethodName'</span><span class="sxs-lookup"><span data-stu-id="c9280-150">Failed to invoke hub method 'MethodName'</span></span>*
* *<span data-ttu-id="c9280-151">Falha na Conexão ao Redis</span><span class="sxs-lookup"><span data-stu-id="c9280-151">Connection to Redis failed</span></span>*

<span data-ttu-id="c9280-152">O SignalR não armazena em buffer as mensagens para enviá-las quando o servidor de volta a funcionar.</span><span class="sxs-lookup"><span data-stu-id="c9280-152">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="c9280-153">Todas as mensagens enviadas enquanto o servidor Redis estiver inativo serão perdidas.</span><span class="sxs-lookup"><span data-stu-id="c9280-153">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="c9280-154">O SignalR se reconecta automaticamente quando o servidor Redis estiver disponível novamente.</span><span class="sxs-lookup"><span data-stu-id="c9280-154">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="c9280-155">Comportamento personalizado para falhas de conexão</span><span class="sxs-lookup"><span data-stu-id="c9280-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="c9280-156">Aqui está um exemplo que mostra como manipular eventos de falha de conexão do Redis.</span><span class="sxs-lookup"><span data-stu-id="c9280-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="clustering"></a><span data-ttu-id="c9280-157">Clustering</span><span class="sxs-lookup"><span data-stu-id="c9280-157">Clustering</span></span>

<span data-ttu-id="c9280-158">O clustering é um método para alcançar alta disponibilidade por meio de vários servidores do Redis.</span><span class="sxs-lookup"><span data-stu-id="c9280-158">Clustering is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="c9280-159">Clustering não é oficialmente suportado, mas pode funcionar.</span><span class="sxs-lookup"><span data-stu-id="c9280-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c9280-160">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="c9280-160">Next steps</span></span>

<span data-ttu-id="c9280-161">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="c9280-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="c9280-162">Documentação do redis</span><span class="sxs-lookup"><span data-stu-id="c9280-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="c9280-163">Documentação do StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="c9280-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="c9280-164">Documentação do Cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="c9280-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
