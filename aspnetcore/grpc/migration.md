---
title: Migrar Serviços gRPC C-core para o ASP.NET Core
author: juntaoluo
description: Saiba como mover um aplicativo de gRPC baseados em C-core existente para ser executado na parte superior da pilha do ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 47d74edd821124f0c8390d704ca7931b7eb6c4cd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895233"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrar Serviços gRPC C-core para o ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

Devido à implementação da pilha subjacente, nem todos os recursos funcionam da mesma maneira entre [gRPC baseado em núcleo C](https://grpc.io/blog/grpc-stacks) aplicativos e aplicativos baseados no ASP.NET Core. Este documento destaca as diferenças principais para migrar entre as duas pilhas.

## <a name="grpc-service-implementation-lifetime"></a>tempo de vida de implementação de serviço gRPC

Na pilha do ASP.NET Core, serviços de gRPC, por padrão, são criados com um [tempo de vida com escopo](xref:fundamentals/dependency-injection#service-lifetimes). Em contraste, gRPC C-core por padrão é associado a um serviço com um [tempo de vida singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Um tempo de vida com escopo permite que a implementação do serviço resolver os outros serviços com tempos de vida com escopo definido. Por exemplo, um tempo de vida com escopo também pode resolver `DBContext` do contêiner de DI por meio da injeção de construtor. Usando o tempo de vida com escopo definido:

* Uma nova instância da implementação do serviço é construída para cada solicitação.
* Não é possível compartilhar o estado entre as solicitações por meio de membros de instância no tipo de implementação.
* A expectativa é armazenar estados compartilhados em um serviço singleton no contêiner de injeção de dependência. Os estados compartilhados armazenados são resolvidos no construtor da implementação do serviço gRPC.

Para obter mais informações sobre os tempos de vida do serviço, consulte <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Adicionar um serviço singleton

Para facilitar a transição de uma implementação de C-core gRPC para ASP.NET Core, é possível alterar o tempo de vida da implementação do serviço do serviço com escopo para singleton. Isso envolve a adição de uma instância da implementação do serviço para o contêiner de injeção de dependência:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

No entanto, uma implementação de serviço com um tempo de vida singleton não é mais capaz de resolver os serviços com escopo por meio da injeção de construtor.

## <a name="configure-grpc-services-options"></a>Configurar opções de serviços gRPC

Em aplicativos baseados em C-core, configurações, como `grpc.max_receive_message_length` e `grpc.max_send_message_length` são configurados com `ChannelOption` quando [construindo a instância de servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

No ASP.NET Core, gRPC fornece configuração por meio de `GrpcServiceOptions` tipo. Por exemplo, um gRPC serviço o tamanho máximo de mensagem de entrada pode ser configurado por meio de `AddGrpc`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

Para obter mais informações sobre a configuração, consulte <xref:grpc/configuration>.

## <a name="logging"></a>Registrando em log

Aplicativos baseados em C-core contam com o `GrpcEnvironment` à [configurar o agente de log](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) para fins de depuração. A pilha do ASP.NET Core fornece essa funcionalidade por meio de [API de registro em log](xref:fundamentals/logging/index). Por exemplo, um agente de log pode ser adicionado ao serviço gRPC por meio da injeção de construtor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Aplicativos baseados em C-core configurar HTTPS por meio de [Server.Ports propriedade](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Um conceito semelhante é usado para configurar os servidores no ASP.NET Core. Por exemplo, o Kestrel usa [configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration) para essa funcionalidade.

## <a name="interceptors-and-middleware"></a>Interceptores e Middleware

ASP.NET Core [middleware](xref:fundamentals/middleware/index) oferece funcionalidades semelhantes em comparação comparadas interceptores em aplicativos baseados em C-core gRPC. Middleware e interceptores são conceitualmente as mesmas como ambos são usados para construir um pipeline que manipula uma solicitação gRPC. Ambos permitem que o trabalho seja realizado antes ou após o próximo componente no pipeline. No entanto, o middleware do ASP.NET Core opera nas mensagens HTTP/2 subjacentes, enquanto interceptores operam na camada de abstração usando gRPC a [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
