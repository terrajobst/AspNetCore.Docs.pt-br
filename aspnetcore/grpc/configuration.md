---
title: gRPC para configuração do ASP.NET Core
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 5/30/2019
uid: grpc/configuration
ms.openlocfilehash: 1f8250dc9aa8b82da384ee28287011baa19dc11f
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491239"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="03345-103">gRPC para configuração do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03345-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="03345-104">Configurar opções de serviços</span><span class="sxs-lookup"><span data-stu-id="03345-104">Configure services options</span></span>

<span data-ttu-id="03345-105">A tabela a seguir descreve as opções de configuração gRPC serviços:</span><span class="sxs-lookup"><span data-stu-id="03345-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="03345-106">Opção</span><span class="sxs-lookup"><span data-stu-id="03345-106">Option</span></span> | <span data-ttu-id="03345-107">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="03345-107">Default Value</span></span> | <span data-ttu-id="03345-108">Descrição</span><span class="sxs-lookup"><span data-stu-id="03345-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="03345-109">O tamanho máximo da mensagem em bytes, que podem ser enviados do servidor.</span><span class="sxs-lookup"><span data-stu-id="03345-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="03345-110">Tentativa de enviar uma mensagem que excede os resultados de tamanho máximo de mensagem configurado em uma exceção.</span><span class="sxs-lookup"><span data-stu-id="03345-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="03345-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="03345-111">4 MB</span></span> | <span data-ttu-id="03345-112">O tamanho máximo da mensagem em bytes, que podem ser recebidas pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="03345-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="03345-113">Se o servidor recebe uma mensagem que exceda esse limite, ele gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="03345-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="03345-114">Aumentar esse valor permite que o servidor receber mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="03345-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="03345-115">Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de serviço.</span><span class="sxs-lookup"><span data-stu-id="03345-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="03345-116">O padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="03345-116">The default is `false`.</span></span> <span data-ttu-id="03345-117">Definindo `EnableDetailedErrors` para `true` pode deixar vazar informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="03345-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="03345-118">gzip</span><span class="sxs-lookup"><span data-stu-id="03345-118">gzip</span></span> | <span data-ttu-id="03345-119">Uma coleção de provedores de compactação usado para compactar e descompactar mensagens.</span><span class="sxs-lookup"><span data-stu-id="03345-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="03345-120">Provedores de compactação personalizado podem ser criados e adicionados à coleção.</span><span class="sxs-lookup"><span data-stu-id="03345-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="03345-121">O padrão configurado o provedor oferece suporte à **gzip** compactação.</span><span class="sxs-lookup"><span data-stu-id="03345-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="03345-122">O algoritmo de compactação usado para compactar mensagens enviadas do servidor.</span><span class="sxs-lookup"><span data-stu-id="03345-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="03345-123">O algoritmo deve corresponder a um provedor de compactação no `CompressionProviders`.</span><span class="sxs-lookup"><span data-stu-id="03345-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="03345-124">Para o algoritmo compactar uma resposta, o cliente deve indicar oferece suporte ao algoritmo, enviando-as **grpc-codificação aceita** cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="03345-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="03345-125">O nível de compactação usado para compactar mensagens enviadas do servidor.</span><span class="sxs-lookup"><span data-stu-id="03345-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="03345-126">Opções podem ser configuradas para todos os serviços, fornecendo um delegado de opções para o `AddGrpc` chamar em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="03345-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="03345-127">Opções para um único serviço substituem as opções de globais fornecidas no `AddGrpc` e pode ser configurado usando `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="03345-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="03345-128">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="03345-128">Configure client options</span></span>

<span data-ttu-id="03345-129">O código a seguir define o envio de máximo de cliente e o tamanho de mensagem de recebimento:</span><span class="sxs-lookup"><span data-stu-id="03345-129">The following code sets the client maximum send and receive message size:</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a><span data-ttu-id="03345-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="03345-130">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
