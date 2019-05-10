---
title: Introdução ao gRPC no ASP.NET Core
author: juntaoluo
description: Saiba mais sobre os serviços de gRPC com a pilha do ASP.NET Core e o servidor Kestrel.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085548"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a><span data-ttu-id="7c9b6-103">Introdução ao gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c9b6-103">Introduction to gRPC on ASP.NET Core</span></span>

<span data-ttu-id="7c9b6-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="7c9b6-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="7c9b6-105">[gRPC](https://grpc.io/docs/guides/) é uma estrutura de RPC (Chamada de Procedimento Remoto) de linguagem independente de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span> <span data-ttu-id="7c9b6-106">Para obter mais informações sobre os conceitos básicos de gRPC, confira a [página de documentação gRPC](https://grpc.io/docs/).</span><span class="sxs-lookup"><span data-stu-id="7c9b6-106">For more on gRPC fundamentals, see the [gRPC documentation page](https://grpc.io/docs/).</span></span>

<span data-ttu-id="7c9b6-107">Os principais benefícios de gRPC são:</span><span class="sxs-lookup"><span data-stu-id="7c9b6-107">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="7c9b6-108">Moderna estrutura leve de RPC de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-108">Modern high-performance lightweight RPC framework.</span></span>
* <span data-ttu-id="7c9b6-109">Desenvolvimento da API de primeiro contrato, usando buffers de protocolo, por padrão, permitindo implementações independente de linguagem.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-109">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="7c9b6-110">As ferramentas disponíveis para várias linguagens gerarem clientes e servidores fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-110">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="7c9b6-111">Dá suporte ao cliente, servidor e chamadas bi-direcionais de streaming.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-111">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="7c9b6-112">Uso de rede reduzida com a serialização binária Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-112">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="7c9b6-113">Esses benefícios tornam o gRPC ideal para:</span><span class="sxs-lookup"><span data-stu-id="7c9b6-113">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="7c9b6-114">Microsserviços leves em que a eficiência é crítica.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-114">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="7c9b6-115">Sistemas poliglotas nos quais múltiplas linguagens são necessárias para o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-115">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="7c9b6-116">Serviços ponto a ponto em tempo real que precisam lidar com solicitações ou respostas de streaming.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-116">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

<span data-ttu-id="7c9b6-117">Enquanto uma implementação C# está disponível atualmente na [página oficial do gRPC](https://grpc.io/docs/quickstart/csharp.html), a implementação atual se baseia na biblioteca nativa escrita em C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)).</span><span class="sxs-lookup"><span data-stu-id="7c9b6-117">While a C# implementation is currently available on the official [gRPC page](https://grpc.io/docs/quickstart/csharp.html), the current implementation relies on the native library written in C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)).</span></span> <span data-ttu-id="7c9b6-118">O trabalho está atualmente em andamento para fornecer uma nova implementação com base em servidor Kestrel HTTP e a pilha do ASP.NET Core que é totalmente gerenciada.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-118">Work is currently in progress to provide a new implementation based on the Kestrel HTTP server and the ASP.NET Core stack that is fully managed.</span></span> <span data-ttu-id="7c9b6-119">Os seguintes documentos fornecem uma introdução à criação de serviços de gRPC com esta nova implementação.</span><span class="sxs-lookup"><span data-stu-id="7c9b6-119">The following documents provide an introduction to building gRPC services with this new implementation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c9b6-120">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7c9b6-120">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>