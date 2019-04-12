---
title: Comparando serviços gRPC com APIs HTTP
author: jamesnk
description: Saiba como gRPC compara com APIs de HTTP e o que ele tem recomendável cenários são.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/31/2019
uid: grpc/comparison
ms.openlocfilehash: 0e9ef0e7ca8fb6d847b45f6dd7bd0aaa35fd149f
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515370"
---
# <a name="comparing-grpc-services-with-http-apis"></a>Comparando serviços gRPC com APIs HTTP

Por [James Newton-King](https://twitter.com/jamesnk)

Este artigo explica como [services gRPC](https://grpc.io/docs/guides/) comparado às APIs de HTTP (incluindo o ASP.NET Core [APIs da Web](xref: web-api/index)). A tecnologia usada para fornecer uma API para seu aplicativo é uma opção importante e gRPC oferece benefícios exclusivos em comparação comparados APIs de HTTP. Este artigo discute os pontos fortes e fracos do gRPC e recomenda a cenários de uso gRPC sobre outras tecnologias.

#### <a name="overview"></a>Visão geral

|    Recurso             |    gRPC                                                 |    APIs de HTTP com JSON                       |
|------------------------|---------------------------------------------------------|----------------------------------------------|
|    Contrato            |    Necessário (`*.proto`)                                 |    Opcional (OpenAPI)                        |
|    Transporte           |    HTTP/2                                               |    HTTP                                      |
|    carga             |    [Protobuf (pequeno, binário)](#performance)             |    JSON (grande, humanamente legível)              |
|    Prescriptiveness    |    [Especificação Strict](#strict-specification)        |    Flexível. Qualquer HTTP é válido                  |
|    Streaming           |    [Cliente, servidor, bi-direcional](#streaming)         |    Cliente, servidor                            |
|    Suporte ao navegador     |    [Não (requer web grpc)](#limited-browser-support)   |    Sim                                       |
|    Segurança            |    Transporte (HTTPS)                                    |    Transporte (HTTPS)                         |
|    Geração de código do cliente     |    [Sim](#code-generation)                              |    OpenAPI + ferramentas de terceiros             |

## <a name="grpc-strengths"></a>gRPC strengths

### <a name="performance"></a>Desempenho

gRPC mensagens são serializadas usando [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), um formato de mensagem binária eficiente. Protobuf serializa muito rapidamente no servidor e cliente. Protobuf serialização resulta em cargas de mensagem pequena, importantes em cenários de largura de banda limitada, como aplicativos móveis.

gRPC destina-se de uma revisão principal de HTTP que fornece benefícios de desempenho significativa sobre HTTP HTTP/2, 1. x:

* O enquadramento binário e compactação. Protocolo HTTP/2 é compacto e eficiente no envio e recebimento.
* Multiplexação de várias chamadas HTTP/2 ao longo de uma única conexão de TCP. Multiplexação elimina [cabeçalho de linha de bloqueio](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Geração de código

Todas as estruturas de gRPC fornecem suporte de primeira classe para geração de código. Um arquivo de núcleo para o desenvolvimento de gRPC é o [ `*.proto` arquivo](https://developers.google.com/protocol-buffers/docs/proto3), que define o contrato de serviços de gRPC e mensagens. De estruturas de gRPC esse arquivo código gerará uma classe base do serviço, mensagens e um cliente completa.

Compartilhando o `*.proto` arquivo entre servidor e cliente, mensagens e o código do cliente pode ser gerado no final, ao final. Geração de código do cliente elimina a duplicação de mensagens no cliente e no servidor e cria um cliente fortemente tipados para você. Não precisar escrever um cliente economiza o tempo de desenvolvimento significativo em aplicativos com muitos serviços.

### <a name="strict-specification"></a>Especificação Strict

Não existe uma especificação formal para a API de HTTP com JSON. Os desenvolvedores debater o melhor formato de URLs, verbos HTTP e códigos de resposta.

O [gRPC especificação](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) é prescritivo sobre o formato de um serviço gRPC deve seguir. gRPC elimina o debate e economiza tempo de desenvolvedor porque gPRC é consistente entre plataformas e implementações.

### <a name="streaming"></a>Streaming

HTTP/2 fornece uma base para fluxos de comunicação em tempo real, de longa duração. gRPC fornece suporte de primeira classe para transmissão através de HTTP/2.

Um serviço gRPC dá suporte a todas as combinações de streaming:

* Unário (nenhum streaming)
* Servidor para o cliente de streaming
* Cliente para servidor de streaming
* Streaming de BI-direcional

### <a name="deadlinetimeouts-and-cancellation"></a>Prazo final/tempos limite e cancelamento

gRPC permite que os clientes especificar quanto tempo eles estão dispostos a esperar para que uma RPC ser concluída. O [prazo](https://grpc.io/blog/deadlines) é enviada ao servidor, e o servidor pode decidir qual ação será tomada se ele exceder o prazo final. Por exemplo, o servidor pode cancelar solicitações de gRPC/HTTP/banco de dados em andamento no tempo limite.

Propagação de prazo e cancelamento por meio de filho gRPC chamadas ajuda a impor limites de uso de recursos.

## <a name="grpc-recommended-scenarios"></a>gRPC recomendado cenários

gRPC é adequado para os seguintes cenários:

* **Microsserviços** &ndash; gRPC é projetado de baixo latência e alta comunicação de produtividade. gRPC é ótimo para microsserviços leves em que a eficiência é crítica.
* **Comunicação em tempo real de ponto a ponto** &ndash; gRPC tem excelente suporte para streaming de bi-direcional. gRPC serviços podem enviar por push mensagens tempo real sem a sondagem.
* **Ambientes de Polygot** &ndash; gRPC ferramentas dá suporte a todas as linguagens de desenvolvimento populares, tornando gRPC uma boa opção para ambientes de vários idiomas.
* **Rede de ambientes restritos** &ndash; gRPC mensagens são serializadas com Protobuf, um formato de mensagem leve. Uma mensagem gRPC é sempre menor do que uma mensagem JSON equivalente.

## <a name="grpc-weaknesses"></a>gRPC fracos

### <a name="limited-browser-support"></a>Suporte ao navegador limitado

Não é possível chamar diretamente de um serviço gRPC em um navegador hoje mesmo. gRPC usa intensamente os recursos HTTP/2 e nenhum navegador fornece o nível de controle necessário sobre solicitações da web para dar suporte a um cliente gRPC. Por exemplo, navegadores não permitir que um chamador requer que o HTTP/2 ser usado ou fornecer acesso aos quadros HTTP/2 subjacentes.

[gRPC Web](https://grpc.io/docs/tutorials/basic/web.html) é uma tecnologia adicional da equipe do gRPC que fornece suporte limitado gRPC no navegador. gRPC Web consiste em duas partes: um cliente JavaScript que dá suporte a todos os navegadores modernos e um proxy Web gRPC no servidor. O cliente Web gRPC chama o proxy e encaminhará o proxy nas solicitações ao servidor gRPC gRPC.

Nem todos os recursos do gRPC têm suporte pela Web gRPC. Cliente e o streaming de bi-direcional não tem suporte e não há suporte limitado para o servidor de streaming.

### <a name="not-human-readable"></a>Não legível

Solicitações da API HTTP são enviadas como texto e podem ser lidos e criadas por humanos.

Por padrão, mensagens gRPC são codificadas com Protobuf. Embora Protobuf é eficiente para enviar e receber, seu formato binário não é humano legível. Protobuf requer especificada na descrição da interface da mensagem a `*.proto` arquivo a ser desserializado corretamente. Ferramentas adicionais são necessária para analisar cargas Protobuf durante a transmissão e compor solicitações manualmente.

Recursos como [reflexão do servidor](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) e o [ferramenta de linha de comando gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existe para ajudá-lo com mensagens de binários Protobuf. Além disso, o suporte de mensagens Protobuf [conversão para e do JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). A conversão de JSON interna fornece uma maneira eficiente para converter mensagens Protobuf em formato legível durante a depuração.

## <a name="alternative-framework-scenarios"></a>Cenários de framework alternativo

Outras estruturas são recomendadas para gRPC nos seguintes cenários:

* **APIs de navegador acessíveis** &ndash; gRPC totalmente não tem suporte no navegador. gRPC Web pode oferecer suporte ao navegador, mas tem limitações e apresenta um proxy do servidor.
* **Comunicação em tempo real de difusão** &ndash; gRPC dá suporte à comunicação em tempo real por meio de streaming, mas o conceito de transmitir uma mensagem de saída para conexões registradas não existe. Por exemplo em um cenário de sala de bate-papo onde novas mensagens de bate-papo devem ser enviadas a todos os clientes na sala de bate-papo, cada chamada gRPC é necessário para transmitir individualmente novas mensagens de bate-papo ao cliente. [O SignalR](xref:signalr/introduction) é uma estrutura útil para esse cenário. O SignalR tem o conceito de conexões persistentes e o suporte interno para mensagens de difusão.
* **Entre processos de comunicação** &ndash; um processo deve hospedar um servidor HTTP/2 para aceitar chamadas gRPC. Para Windows, a comunicação entre processos [pipes](/dotnet/standard/io/pipe-operations) é um método rápido e leve de comunicação.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
