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
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC para configuração do ASP.NET Core

## <a name="configure-services-options"></a>Configurar opções de serviços

A tabela a seguir descreve as opções de configuração gRPC serviços:

| Opção | Valor padrão | Descrição |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | O tamanho máximo da mensagem em bytes, que podem ser enviados do servidor. Tentativa de enviar uma mensagem que excede os resultados de tamanho máximo de mensagem configurado em uma exceção. |
| `ReceiveMaxMessageSize` | 4 MB | O tamanho máximo da mensagem em bytes, que podem ser recebidas pelo servidor. Se o servidor recebe uma mensagem que exceda esse limite, ele gerará uma exceção. Aumentar esse valor permite que o servidor receber mensagens maiores, mas pode afetar negativamente o consumo de memória. |
| `EnableDetailedErrors` | `false` | Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de serviço. O padrão é `false`. Definir isso como `true` pode deixar vazar informações confidenciais. |
| `CompressionProviders` | gzip | Uma coleção de provedores de compactação usado para compactar e descompactar mensagens. Provedores de compactação personalizado podem ser criados e adicionados à coleção. O padrão configurado o provedor oferece suporte à **gzip** compactação. |
| `ResponseCompressionAlgorithm` | `null` | O algoritmo de compactação usado para compactar mensagens enviadas do servidor. O algoritmo deve corresponder a um provedor de compactação no `CompressionProviders`. Para o algorthm compactar uma resposta do cliente deve indicar oferece suporte ao algoritmo, enviando-as **grpc-codificação aceita** cabeçalho. |
| `ResponseCompressionLevel` | `null` | O nível de compactação usado para compactar mensagens enviadas do servidor. |

Opções podem ser configuradas para todos os serviços, fornecendo um delegado de opções para o `AddGrpc` chamar em `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

Opções para um único serviço substituem as opções de globais fornecidas no `AddGrpc` e pode ser configurado usando `AddServiceOptions<TService>`:

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
