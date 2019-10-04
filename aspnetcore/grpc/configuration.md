---
title: configuração do gRPC para .NET
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: 3ef92f10d914ef9fa3e13a7bdd5c863bab297f57
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925213"
---
# <a name="grpc-for-net-configuration"></a>configuração do gRPC para .NET

## <a name="configure-services-options"></a>Configurar opções de serviços

os serviços gRPCs são `AddGrpc` configurados com o no *Startup.cs*. A tabela a seguir descreve as opções para configurar os serviços gRPCs:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do servidor. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| `MaxReceiveMessageSize` | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo servidor. Se o servidor receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de serviço. O padrão é `false`. A `EnableDetailedErrors` configuração `true` para pode vazar informações confidenciais. |
| `CompressionProviders` | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |
| `ResponseCompressionAlgorithm` | `null` | O algoritmo de compactação usado para compactar mensagens enviadas do servidor. O algoritmo deve corresponder a um provedor de `CompressionProviders`compactação no. Para que o algoritmo compacte uma resposta, o cliente deve indicar que ele dá suporte ao algoritmo enviando-o no cabeçalho **grpc-Accept-Encoding** . |
| `ResponseCompressionLevel` | `null` | O nível de compactação usado para compactar mensagens enviadas do servidor. |

As opções podem ser configuradas para todos os serviços, fornecendo um `AddGrpc` delegado de `Startup.ConfigureServices`opções para a chamada em:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

As opções para um único serviço substituem as opções globais `AddGrpc` fornecidas no e podem ser `AddServiceOptions<TService>`configuradas usando:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurar opções do cliente

a configuração do cliente gRPC está `GrpcChannelOptions`definida em. A tabela a seguir descreve as opções para configurar os canais gRPC:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `HttpClient` | Nova instância | O `HttpClient` usado para fazer chamadas gRPC. Um cliente pode ser definido para configurar um personalizado `HttpClientHandler`ou adicionar manipuladores adicionais ao pipeline http para chamadas gRPC. Se não `HttpClient` for especificado, uma nova `HttpClient` instância será criada para o canal. Ele será descartado automaticamente. |
| `DisposeHttpClient` | `false` | Se `true`e um `HttpClient` for especificado, a `HttpClient` instância será descartada quando o `GrpcChannel` for descartado. |
| `LoggerFactory` | `null` | O `LoggerFactory` usado pelo cliente para registrar informações sobre chamadas gRPC. Uma `LoggerFactory` instância pode ser resolvida da injeção de dependência ou `LoggerFactory.Create`criada usando. Para obter exemplos de configuração de registro <xref:grpc/diagnostics#grpc-client-logging>em log, consulte. |
| `MaxSendMessageSize` | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do cliente. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| `MaxReceiveMessageSize` | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo cliente. Se o cliente receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o cliente receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `Credentials` | `null` | Uma instância de `ChannelCredentials`. As credenciais são usadas para adicionar metadados de autenticação a chamadas gRPC. |
| `CompressionProviders` | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |

O código a seguir:

* Define o tamanho máximo de mensagens de envio e recebimento no canal.
* Cria um cliente.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
