---
title: gRPC para configuração do ASP.NET Core
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087339"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="f8ba1-103">gRPC para configuração do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8ba1-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="f8ba1-104">Configurar opções de serviços</span><span class="sxs-lookup"><span data-stu-id="f8ba1-104">Configure services options</span></span>

<span data-ttu-id="f8ba1-105">A tabela a seguir descreve as opções de configuração gRPC serviços:</span><span class="sxs-lookup"><span data-stu-id="f8ba1-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="f8ba1-106">Opção</span><span class="sxs-lookup"><span data-stu-id="f8ba1-106">Option</span></span> | <span data-ttu-id="f8ba1-107">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="f8ba1-107">Default Value</span></span> | <span data-ttu-id="f8ba1-108">Descrição</span><span class="sxs-lookup"><span data-stu-id="f8ba1-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="f8ba1-109">O tamanho máximo da mensagem em bytes, que podem ser enviados do servidor.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="f8ba1-110">Tentativa de enviar uma mensagem que excede os resultados de tamanho máximo de mensagem configurado em uma exceção.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="f8ba1-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="f8ba1-111">4 MB</span></span> | <span data-ttu-id="f8ba1-112">O tamanho máximo da mensagem em bytes, que podem ser recebidas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="f8ba1-113">Se o servidor recebe uma mensagem que exceda esse limite, ele gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="f8ba1-114">Aumentar esse valor permite que o servidor receber mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f8ba1-115">Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de serviço.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="f8ba1-116">O padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-116">The default is `false`.</span></span> <span data-ttu-id="f8ba1-117">Definir isso como `true` pode deixar vazar informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="f8ba1-118">gzip</span><span class="sxs-lookup"><span data-stu-id="f8ba1-118">gzip</span></span> | <span data-ttu-id="f8ba1-119">Uma coleção de provedores de compactação usado para compactar e descompactar mensagens.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="f8ba1-120">Provedores de compactação personalizado podem ser criados e adicionados à coleção.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="f8ba1-121">O padrão configurado o provedor oferece suporte à **gzip** compactação.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="f8ba1-122">O algoritmo de compactação usado para compactar mensagens enviadas do servidor.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="f8ba1-123">O algoritmo deve corresponder a um provedor de compactação no `CompressionProviders`.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="f8ba1-124">Para o algorthm compactar uma resposta do cliente deve indicar oferece suporte ao algoritmo, enviando-as **grpc-codificação aceita** cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="f8ba1-125">O nível de compactação usado para compactar mensagens enviadas do servidor.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="f8ba1-126">Opções podem ser configuradas para todos os serviços, fornecendo um delegado de opções para o `AddGrpc` chamar em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="f8ba1-127">Opções para um único serviço substituem as opções de globais fornecidas no `AddGrpc` e pode ser configurado usando `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="f8ba1-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="configure-kestrel-options"></a><span data-ttu-id="f8ba1-128">Configurar opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="f8ba1-128">Configure Kestrel options</span></span>

<span data-ttu-id="f8ba1-129">Servidor kestrel tem opções de configuração que afetam o comportamento do gRPC para ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f8ba1-129">Kestrel server has configuration options that affect the behavior of gRPC for ASP.NET.</span></span>

### <a name="request-body-data-rate-limit"></a><span data-ttu-id="f8ba1-130">Limite de taxa de dados de corpo de solicitação</span><span class="sxs-lookup"><span data-stu-id="f8ba1-130">Request body data rate limit</span></span>

<span data-ttu-id="f8ba1-131">Por padrão, o servidor Kestrel impõe uma [taxa de dados de corpo de solicitação mínimo](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span><span class="sxs-lookup"><span data-stu-id="f8ba1-131">By default, the Kestrel server imposes a [minimum request body data rate](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span></span> <span data-ttu-id="f8ba1-132">Para o cliente de streaming e duplex, chamadas de streaming, essa taxa não pode ser atendida e a conexão pode ser esgotado. O mínimo limite de taxa de dados deve ser desabilitada quando o serviço gRPC inclui o cliente de streaming e duplex, chamadas de streaming do corpo da solicitação:</span><span class="sxs-lookup"><span data-stu-id="f8ba1-132">For client streaming and duplex streaming calls, this rate may not be satisfied and the connection may be timed out. The minimum request body data rate limit must be disabled when the gRPC service includes client streaming and duplex streaming calls:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a><span data-ttu-id="f8ba1-133">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f8ba1-133">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
