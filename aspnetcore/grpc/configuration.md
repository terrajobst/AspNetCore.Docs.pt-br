---
title: gRPC para configuração do ASP.NET Core
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 851c9ca1f7d62f6f368df66bb38eb4bbaf64bf32
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66041889"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="0794b-103">gRPC para configuração do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0794b-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="0794b-104">Configurar opções de serviços</span><span class="sxs-lookup"><span data-stu-id="0794b-104">Configure services options</span></span>

<span data-ttu-id="0794b-105">A tabela a seguir descreve as opções de configuração gRPC serviços:</span><span class="sxs-lookup"><span data-stu-id="0794b-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="0794b-106">Opção</span><span class="sxs-lookup"><span data-stu-id="0794b-106">Option</span></span> | <span data-ttu-id="0794b-107">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="0794b-107">Default Value</span></span> | <span data-ttu-id="0794b-108">Descrição</span><span class="sxs-lookup"><span data-stu-id="0794b-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="0794b-109">O tamanho máximo da mensagem em bytes, que podem ser enviados do servidor.</span><span class="sxs-lookup"><span data-stu-id="0794b-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="0794b-110">Tentativa de enviar uma mensagem que excede os resultados de tamanho máximo de mensagem configurado em uma exceção.</span><span class="sxs-lookup"><span data-stu-id="0794b-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="0794b-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="0794b-111">4 MB</span></span> | <span data-ttu-id="0794b-112">O tamanho máximo da mensagem em bytes, que podem ser recebidas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="0794b-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="0794b-113">Se o servidor recebe uma mensagem que exceda esse limite, ele gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="0794b-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="0794b-114">Aumentar esse valor permite que o servidor receber mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="0794b-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0794b-115">Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de serviço.</span><span class="sxs-lookup"><span data-stu-id="0794b-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="0794b-116">O padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="0794b-116">The default is `false`.</span></span> <span data-ttu-id="0794b-117">Definir isso como `true` pode deixar vazar informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="0794b-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="0794b-118">gzip</span><span class="sxs-lookup"><span data-stu-id="0794b-118">gzip</span></span> | <span data-ttu-id="0794b-119">Uma coleção de provedores de compactação usado para compactar e descompactar mensagens.</span><span class="sxs-lookup"><span data-stu-id="0794b-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="0794b-120">Provedores de compactação personalizado podem ser criados e adicionados à coleção.</span><span class="sxs-lookup"><span data-stu-id="0794b-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="0794b-121">O padrão configurado o provedor oferece suporte à **gzip** compactação.</span><span class="sxs-lookup"><span data-stu-id="0794b-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="0794b-122">O algoritmo de compactação usado para compactar mensagens enviadas do servidor.</span><span class="sxs-lookup"><span data-stu-id="0794b-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="0794b-123">O algoritmo deve corresponder a um provedor de compactação no `CompressionProviders`.</span><span class="sxs-lookup"><span data-stu-id="0794b-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="0794b-124">Para o algorthm compactar uma resposta do cliente deve indicar oferece suporte ao algoritmo, enviando-as **grpc-codificação aceita** cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="0794b-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="0794b-125">O nível de compactação usado para compactar mensagens enviadas do servidor.</span><span class="sxs-lookup"><span data-stu-id="0794b-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="0794b-126">Opções podem ser configuradas para todos os serviços, fornecendo um delegado de opções para o `AddGrpc` chamar em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0794b-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="0794b-127">Opções para um único serviço substituem as opções de globais fornecidas no `AddGrpc` e pode ser configurado usando `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="0794b-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="additional-resources"></a><span data-ttu-id="0794b-128">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0794b-128">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
