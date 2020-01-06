---
title: configuração do gRPC para .NET
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: de478752f94713d7f3d55ed66e6410500c3a72e8
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355726"
---
# <a name="grpc-for-net-configuration"></a>configuração do gRPC para .NET

## <a name="configure-services-options"></a>Configurar opções de serviços

os serviços gRPCs são configurados com `AddGrpc` no *Startup.cs*. A tabela a seguir descreve as opções para configurar os serviços gRPCs:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do servidor. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| `MaxReceiveMessageSize` | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo servidor. Se o servidor receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `EnableDetailedErrors` | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de serviço. O padrão é `false`. Definir `EnableDetailedErrors` como `true` pode vazar informações confidenciais. |
| `CompressionProviders` | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |
| `ResponseCompressionAlgorithm` | `null` | O algoritmo de compactação usado para compactar mensagens enviadas do servidor. O algoritmo deve corresponder a um provedor de compactação no `CompressionProviders`. Para que o algoritmo compacte uma resposta, o cliente deve indicar que ele dá suporte ao algoritmo enviando-o no cabeçalho **grpc-Accept-Encoding** . |
| `ResponseCompressionLevel` | `null` | O nível de compactação usado para compactar mensagens enviadas do servidor. |
| `Interceptors` | {1&gt;Nenhum&lt;1} | Uma coleção de interceptores que são executados com cada chamada gRPC. Os interceptores são executados na ordem em que estão registrados. Os interceptores configurados globalmente são executados antes de interceptadores configurados para um único serviço. Para obter mais informações sobre os interceptores gRPC, consulte [interceptores do gRPC vs. middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

As opções podem ser configuradas para todos os serviços, fornecendo um delegado de opções para a chamada `AddGrpc` no `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

As opções para um único serviço substituem as opções globais fornecidas em `AddGrpc` e podem ser configuradas usando `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurar opções do cliente

a configuração do cliente gRPC está definida em `GrpcChannelOptions`. A tabela a seguir descreve as opções para configurar os canais gRPC:

| Opção | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| `HttpClient` | Nova instância | O `HttpClient` usado para fazer chamadas gRPC. Um cliente pode ser definido para configurar um `HttpClientHandler`personalizado ou adicionar manipuladores adicionais ao pipeline HTTP para chamadas gRPC. Se nenhum `HttpClient` for especificado, uma nova instância de `HttpClient` será criada para o canal. Ele será descartado automaticamente. |
| `DisposeHttpClient` | `false` | Se `true`e um `HttpClient` for especificado, a instância de `HttpClient` será descartada quando o `GrpcChannel` for descartado. |
| `LoggerFactory` | `null` | O `LoggerFactory` usado pelo cliente para registrar informações sobre chamadas gRPC. Uma instância de `LoggerFactory` pode ser resolvida da injeção de dependência ou criada usando `LoggerFactory.Create`. Para obter exemplos de configuração de log, consulte <xref:grpc/diagnostics#grpc-client-logging>. |
| `MaxSendMessageSize` | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do cliente. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| `MaxReceiveMessageSize` | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo cliente. Se o cliente receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o cliente receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `Credentials` | `null` | Uma instância de `ChannelCredentials`. As credenciais são usadas para adicionar metadados de autenticação a chamadas gRPC. |
| `CompressionProviders` | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |

O seguinte código:

* Define o tamanho máximo de mensagens de envio e recebimento no canal.
* Cria um cliente.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
