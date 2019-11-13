---
title: Comparar serviços gRPC com APIs HTTP
author: jamesnk
description: Saiba como o gRPC se compara com as APIs HTTP e o que são cenários recomendadas.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: ceb24d656827548492a6fa326681922297fc481b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963656"
---
# <a name="compare-grpc-services-with-http-apis"></a>Comparar serviços gRPC com APIs HTTP

Por [James Newton – King](https://twitter.com/jamesnk)

Este artigo explica como os [Serviços gRPCs](https://grpc.io/docs/guides/) se comparam às APIs http (incluindo ASP.NET Core [APIs Web](xref:web-api/index)). A tecnologia usada para fornecer uma API para seu aplicativo é uma opção importante, e o gRPC oferece benefícios exclusivos em comparação com as APIs HTTP. Este artigo discute os pontos fortes e fracos do gRPC e recomenda cenários para usar o gRPC em relação a outras tecnologias.

## <a name="high-level-comparison"></a>Comparação de alto nível

A tabela a seguir oferece uma comparação de alto nível dos recursos entre as APIs gRPC e HTTP com JSON.

| Recurso          | gRPC                                               | APIs HTTP com JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Contrato         | Necessário ( *. proto*)                                | Opcional (OpenAPI)            |
| Protocolo         | HTTP/2                                             | HTTP                          |
| Carga          | [Protobuf (pequeno, binário)](#performance)           | JSON (grande, legível por humanos)  |
| Prescriptiveness | [Especificação estrita](#strict-specification)      | Cancelar. Qualquer HTTP é válido.     |
| Streaming        | [Cliente, servidor, bidirecional](#streaming)       | Cliente, servidor                |
| Suporte a navegador  | [Não (requer grpc-Web)](#limited-browser-support) | Sim                           |
| Segurança         | Transporte (TLS)                                    | Transporte (TLS)               |
| Geração de código de cliente | [Sim](#code-generation)                      | OpenAPI + ferramentas de terceiros |

## <a name="grpc-strengths"></a>forças gRPCs

### <a name="performance"></a>Desempenho

as mensagens gRPC são serializadas usando [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), um formato de mensagem binária eficiente. O Protobuf serializa muito rapidamente no servidor e no cliente. A serialização Protobuf resulta em cargas de mensagens pequenas, importantes em cenários de largura de banda limitada, como aplicativos móveis.

o gRPC foi projetado para HTTP/2, uma revisão importante do HTTP que fornece benefícios de desempenho significativos em relação ao HTTP 1. x:

* Enquadramento e compactação binários. O protocolo HTTP/2 é compacto e eficiente no envio e no recebimento.
* Multiplexação de várias chamadas HTTP/2 em uma única conexão TCP. A multiplexação elimina o [bloqueio de cabeçalho de linha](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Geração de código

Todas as estruturas gRPC fornecem suporte de primeira classe para geração de código. Um arquivo de núcleo para o desenvolvimento de gRPC é o [arquivo *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3), que define o contrato de serviços e mensagens do gRPC. Desse arquivo, as estruturas do gRPC de código gerarão uma classe base de serviço, mensagens e um cliente completo.

Ao compartilhar o arquivo *. proto* entre o servidor e o cliente, as mensagens e o código do cliente podem ser gerados de ponta a ponta. A geração de código do cliente elimina a duplicação de mensagens no cliente e no servidor e cria um cliente fortemente tipado para você. Não ter de escrever um cliente economiza um tempo de desenvolvimento significativo em aplicativos com muitos serviços.

### <a name="strict-specification"></a>Especificação estrita

Não existe uma especificação formal para a API HTTP com JSON. Os desenvolvedores debaterão o melhor formato de URLs, verbos HTTP e códigos de resposta.

A [especificação gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) é prescritiva sobre o formato que um serviço gRPC deve seguir. o gRPC elimina o debate e economiza tempo para desenvolvedores porque o gRPC é consistente entre plataformas e implementações.

### <a name="streaming"></a>Streaming

O HTTP/2 fornece uma base para fluxos de comunicação de longa duração e em tempo real. o gRPC fornece suporte de primeira classe para streaming por meio de HTTP/2.

Um serviço gRPC dá suporte A todas as combinações de streaming:

* Unário (sem streaming)
* Streaming de servidor para cliente
* Transmissão de cliente para servidor
* Streaming bidirecional

### <a name="deadlinetimeouts-and-cancellation"></a>Prazo/tempos limite e cancelamento

o gRPC permite que os clientes especifiquem por quanto tempo eles estão dispostos a aguardar a conclusão de um RPC. O [prazo](https://grpc.io/blog/deadlines) é enviado ao servidor e o servidor pode decidir a ação a ser tomada se exceder o prazo. Por exemplo, o servidor pode cancelar solicitações de gRPC/HTTP/banco de dados em andamento no tempo limite.

Propagar o prazo e o cancelamento por meio de chamadas gRPC filho ajuda a impor limites de uso de recursos.

## <a name="grpc-recommended-scenarios"></a>cenários gRPC recomendados

o gRPC é bem adequado para os seguintes cenários:

* Os **microserviços** &ndash; gRPC foi projetado para comunicação de baixa latência e alta taxa de transferência. o gRPC é ótimo para microserviços leves em que a eficiência é fundamental.
* **A comunicação em tempo real de ponto a ponto** &ndash; gRPC tem excelente suporte para streaming bidirecional. os serviços gRPCs podem enviar mensagens por push em tempo real sem sondagem.
* Os **ambientes poliglota** &ndash; ferramentas de gRPC dão suporte a todas as linguagens de desenvolvimento populares, tornando o gRPC uma boa opção para ambientes com vários idiomas.
* **Ambientes com restrição de rede** &ndash; mensagens gRPC são serializadas com Protobuf, um formato de mensagem leve. Uma mensagem gRPC é sempre menor do que uma mensagem JSON equivalente.

## <a name="grpc-weaknesses"></a>gRPC fracos

### <a name="limited-browser-support"></a>Suporte limitado a navegador

É impossível chamar diretamente um serviço gRPC de um navegador hoje. gRPC usa intensamente recursos HTTP/2 e nenhum navegador fornece o nível de controle necessário em relação a solicitações da Web para dar suporte a um cliente gRPC. Por exemplo, os navegadores não permitem que um chamador exija que o HTTP/2 seja usado ou forneça acesso a quadros HTTP/2 subjacentes.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) é uma tecnologia adicional da equipe do gRPC que fornece suporte limitado a gRPC no navegador. gRPC-Web consiste em duas partes: um cliente JavaScript que dá suporte a todos os navegadores modernos e um proxy gRPC-Web no servidor. O cliente Web gRPC chama o proxy e o proxy será encaminhado nas solicitações gRPC para o servidor gRPC.

Nem todos os recursos de gRPC têm suporte no gRPC-Web. Não há suporte para o streaming bidirecional e de cliente, e há suporte limitado para streaming de servidor.

### <a name="not-human-readable"></a>Não legível por humanos

As solicitações de API HTTP são enviadas como texto e podem ser lidas e criadas por humanos.

as mensagens gRPC são codificadas com Protobuf por padrão. Embora o Protobuf seja eficiente para enviar e receber, seu formato binário não é legível por humanos. Protobuf requer a descrição da interface da mensagem especificada no arquivo *. proto* para desserializar corretamente. Ferramentas adicionais são necessárias para analisar cargas de Protobuf na transmissão e para compor solicitações manualmente.

Recursos como [reflexão de servidor](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) e a [ferramenta de linha de comando gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existem para auxiliar com mensagens Protobuf binárias. Além disso, as mensagens Protobuf dão suporte à [conversão de e para JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). A conversão interna de JSON fornece uma maneira eficiente de converter mensagens Protobuf de e para o formulário legível por humanos durante a depuração.

## <a name="alternative-framework-scenarios"></a>Cenários de estrutura alternativos

Outras estruturas são recomendadas em relação ao gRPC nos seguintes cenários:

* **APIs acessíveis ao navegador** &ndash; gRPC não tem suporte total no navegador. o gRPC-Web pode oferecer suporte a navegador, mas tem limitações e introduz um proxy de servidor.
* **Transmita a comunicação em tempo real** &ndash; gRPC dá suporte à comunicação em tempo real via streaming, mas o conceito de difusão de uma mensagem para conexões registradas não existe. Por exemplo, em um cenário de sala de chat em que novas mensagens de chat devem ser enviadas a todos os clientes na sala de chat, cada chamada de gRPC é necessária para transmitir individualmente novas mensagens de chat para o cliente. [SignalR](xref:signalr/introduction) é uma estrutura útil para esse cenário. SignalR tem o conceito de conexões persistentes e suporte interno para mensagens de difusão.
* A **comunicação entre processos** &ndash; um processo deve hospedar um servidor http/2 para aceitar chamadas gRPC de entrada. Para o Windows, os [pipes](/dotnet/standard/io/pipe-operations) de comunicação entre processos são um método rápido e leve de comunicação.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
