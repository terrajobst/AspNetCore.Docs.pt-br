---
title: gRPC para configuração de ASP.NET Core
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/21/2019
uid: grpc/configuration
ms.openlocfilehash: 34eb598211c87fbb2c68ae5e041da50d02f543f7
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310312"
---
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC para configuração de ASP.NET Core

## <a name="configure-services-options"></a>Configurar opções de serviços

A tabela a seguir descreve as opções para configurar os serviços gRPCs:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do servidor. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| `MaxReceiveMessageSize` | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo servidor. Se o servidor receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de serviço. O padrão é `false`. A `EnableDetailedErrors` configuração `true` para pode vazar informações confidenciais. |
| `CompressionProviders` | gzip, deflate | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** e **deflate** . |
| `ResponseCompressionAlgorithm` | `null` | O algoritmo de compactação usado para compactar mensagens enviadas do servidor. O algoritmo deve corresponder a um provedor de `CompressionProviders`compactação no. Para que o algoritmo compacte uma resposta, o cliente deve indicar que ele dá suporte ao algoritmo enviando-o no cabeçalho **grpc-Accept-Encoding** . |
| `ResponseCompressionLevel` | `null` | O nível de compactação usado para compactar mensagens enviadas do servidor. |

As opções podem ser configuradas para todos os serviços, fornecendo um `AddGrpc` delegado de `Startup.ConfigureServices`opções para a chamada em:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

As opções para um único serviço substituem as opções globais `AddGrpc` fornecidas no e podem ser `AddServiceOptions<TService>`configuradas usando:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurar opções do cliente

A tabela a seguir descreve as opções para configurar os canais gRPC:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `HttpClient` | Nova instância | O `HttpClient` usado para fazer chamadas gRPC. Um cliente pode ser definido para configurar um personalizado `HttpClientHandler`ou adicionar manipuladores adicionais ao pipeline http para chamadas gRPC. Por padrão, uma `HttpClient` nova instância é criada. |
| `MaxSendMessageSize` | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do cliente. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| `MaxReceiveMessageSize` | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo cliente. Se o servidor receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `TransportOptions` | `null` | As opções de transporte configuram como o canal chama o serviço gRPC. Atualmente, a única implementação `HttpClientTransport` é que as opções permitem `HttpClient` especificar o usado pelo gRPC. |
| `Credentials` | `null` | Uma instância de `ChannelCredentials`. As credenciais são usadas para adicionar metadados de autenticação a chamadas gRPC. |
| `CompressionProviders` | gzip, deflate | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** e **deflate** . |

O código a seguir:

* Define o tamanho máximo de mensagens de envio e recebimento no canal.
* Cria um cliente.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
