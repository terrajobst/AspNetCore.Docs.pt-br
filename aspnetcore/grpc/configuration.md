---
title: configuração do gRPC para .NET
author: jamesnk
description: Saiba como configurar o gRPC para aplicativos .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: cabe2d86f535bf3063dd7ede9e8a3bc5de70e244
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666897"
---
# <a name="grpc-for-net-configuration"></a>configuração do gRPC para .NET

## <a name="configure-services-options"></a>Configurar opções de serviços

os serviços gRPCs são configurados com `AddGrpc` no *Startup.cs*. A tabela a seguir descreve as opções para configurar os serviços gRPCs:

| {1&gt;Opção&lt;1} | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do servidor. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| MaxReceiveMessageSize | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo servidor. Se o servidor receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| EnableDetailedErrors | `false` | Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de serviço. O padrão é `false`. Definir `EnableDetailedErrors` como `true` pode vazar informações confidenciais. |
| CompressionProviders | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | O algoritmo de compactação usado para compactar mensagens enviadas do servidor. O algoritmo deve corresponder a um provedor de compactação no `CompressionProviders`. Para que o algoritmo compacte uma resposta, o cliente deve indicar que ele dá suporte ao algoritmo enviando-o no cabeçalho **grpc-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | O nível de compactação usado para compactar mensagens enviadas do servidor. |
| Interceptores | Nenhum | Uma coleção de interceptores que são executados com cada chamada gRPC. Os interceptores são executados na ordem em que estão registrados. Os interceptores configurados globalmente são executados antes de interceptadores configurados para um único serviço. Para obter mais informações sobre os interceptores gRPC, consulte [interceptores do gRPC vs. middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

As opções podem ser configuradas para todos os serviços, fornecendo um delegado de opções para a chamada `AddGrpc` no `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

As opções para um único serviço substituem as opções globais fornecidas em `AddGrpc` e podem ser configuradas usando `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurar opções do cliente

a configuração do cliente gRPC está definida em `GrpcChannelOptions`. A tabela a seguir descreve as opções para configurar os canais gRPC:

| {1&gt;Opção&lt;1} | Valor Padrão | Descrição |
| ------ | ------------- | ----------- |
| HttpClient | Nova instância | O `HttpClient` usado para fazer chamadas gRPC. Um cliente pode ser definido para configurar um `HttpClientHandler`personalizado ou adicionar manipuladores adicionais ao pipeline HTTP para chamadas gRPC. Se nenhum `HttpClient` for especificado, uma nova instância de `HttpClient` será criada para o canal. Ele será descartado automaticamente. |
| DisposeHttpClient | `false` | Se `true`e um `HttpClient` for especificado, a instância de `HttpClient` será descartada quando o `GrpcChannel` for descartado. |
| LoggerFactory | `null` | O `LoggerFactory` usado pelo cliente para registrar informações sobre chamadas gRPC. Uma instância de `LoggerFactory` pode ser resolvida da injeção de dependência ou criada usando `LoggerFactory.Create`. Para obter exemplos de configuração de log, consulte <xref:grpc/diagnostics#grpc-client-logging>. |
| MaxSendMessageSize | `null` | O tamanho máximo da mensagem em bytes que pode ser enviado do cliente. A tentativa de enviar uma mensagem que exceda o tamanho máximo da mensagem configurada resultará em uma exceção. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | O tamanho máximo da mensagem em bytes que pode ser recebido pelo cliente. Se o cliente receber uma mensagem que exceda esse limite, ele lançará uma exceção. Aumentar esse valor permite que o cliente receba mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| Credenciais | `null` | Uma instância `ChannelCredentials`. As credenciais são usadas para adicionar metadados de autenticação a chamadas gRPC. |
| CompressionProviders | gzip | Uma coleção de provedores de compactação usados para compactar e descompactar mensagens. Os provedores de compactação personalizados podem ser criados e adicionados à coleção. Os provedores configurados padrão dão suporte à compactação **gzip** . |

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
