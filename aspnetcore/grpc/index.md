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
# <a name="introduction-to-grpc-on-aspnet-core"></a>Introdução ao gRPC no ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

[gRPC](https://grpc.io/docs/guides/) é uma estrutura de RPC (Chamada de Procedimento Remoto) de linguagem independente de alto desempenho. Para obter mais informações sobre os conceitos básicos de gRPC, confira a [página de documentação gRPC](https://grpc.io/docs/).

Os principais benefícios de gRPC são:
* Moderna estrutura leve de RPC de alto desempenho.
* Desenvolvimento da API de primeiro contrato, usando buffers de protocolo, por padrão, permitindo implementações independente de linguagem.
* As ferramentas disponíveis para várias linguagens gerarem clientes e servidores fortemente tipados.
* Dá suporte ao cliente, servidor e chamadas bi-direcionais de streaming.
* Uso de rede reduzida com a serialização binária Protobuf.

Esses benefícios tornam o gRPC ideal para:
* Microsserviços leves em que a eficiência é crítica.
* Sistemas poliglotas nos quais múltiplas linguagens são necessárias para o desenvolvimento.
* Serviços ponto a ponto em tempo real que precisam lidar com solicitações ou respostas de streaming.

Enquanto uma implementação C# está disponível atualmente na [página oficial do gRPC](https://grpc.io/docs/quickstart/csharp.html), a implementação atual se baseia na biblioteca nativa escrita em C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)). O trabalho está atualmente em andamento para fornecer uma nova implementação com base em servidor Kestrel HTTP e a pilha do ASP.NET Core que é totalmente gerenciada. Os seguintes documentos fornecem uma introdução à criação de serviços de gRPC com esta nova implementação.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>