---
title: Migrando serviços gRPCs do C-Core para ASP.NET Core
author: juntaoluo
description: Saiba como mover um aplicativo gRPC baseado em C-Core existente para ser executado no topo da pilha ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664132"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrando serviços gRPCs do C-Core para ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

Devido à implementação da pilha subjacente, nem todos os recursos funcionam da mesma maneira entre os aplicativos [gRPC baseados em C-Core](https://grpc.io/blog/grpc-stacks) e os aplicativos baseados em ASP.NET Core. Este documento destaca as principais diferenças de migração entre as duas pilhas.

## <a name="grpc-service-implementation-lifetime"></a>tempo de vida de implementação do serviço gRPC

Na pilha de ASP.NET Core, os serviços gRPCs, por padrão, são criados com um [tempo de vida no escopo](xref:fundamentals/dependency-injection#service-lifetimes). Por outro lado, o gRPC C-Core é associado por padrão a um serviço com um [tempo de vida singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Um tempo de vida no escopo permite que a implementação do serviço resolva outros serviços com tempos de vida de escopo. Por exemplo, uma vida útil com escopo também pode resolver `DbContext` do contêiner DI por meio de injeção de construtor. Usando tempo de vida no escopo:

* Uma nova instância da implementação do serviço é construída para cada solicitação.
* Não é possível compartilhar o estado entre solicitações por meio de membros de instância no tipo de implementação.
* A expectativa é armazenar Estados compartilhados em um serviço singleton no contêiner DI. Os Estados compartilhados armazenados são resolvidos no construtor da implementação do serviço gRPC.

Para obter mais informações sobre tempos de vida de serviço, consulte <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Adicionar um serviço singleton

Para facilitar a transição de uma implementação gRPC C-Core para ASP.NET Core, é possível alterar o tempo de vida do serviço da implementação do serviço do escopo para singleton. Isso envolve a adição de uma instância da implementação do serviço ao contêiner DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

No entanto, uma implementação de serviço com um tempo de vida singleton não é mais capaz de resolver serviços com escopo por meio de injeção de construtor.

## <a name="configure-grpc-services-options"></a>Configurar opções de serviços gRPCs

Em aplicativos baseados em C-Core, as configurações como `grpc.max_receive_message_length` e `grpc.max_send_message_length` são configuradas com `ChannelOption` ao [construir a instância do servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

No ASP.NET Core, gRPC fornece a configuração por meio do tipo de `GrpcServiceOptions`. Por exemplo, um serviço gRPC o tamanho máximo da mensagem de entrada pode ser configurado via `AddGrpc`. O exemplo a seguir altera o `MaxReceiveMessageSize` padrão de 4 MB para 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Para obter mais informações sobre a configuração, consulte <xref:grpc/configuration>.

## <a name="logging"></a>Registro em log

Os aplicativos baseados em núcleo com base no `GrpcEnvironment` para [Configurar o agente](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) para fins de depuração. A pilha de ASP.NET Core fornece essa funcionalidade por meio da [API de log](xref:fundamentals/logging/index). Por exemplo, um agente de log pode ser adicionado ao serviço gRPC por meio de injeção de construtor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Aplicativos baseados em C-Core configuram HTTPS por meio da [Propriedade Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Um conceito semelhante é usado para configurar servidores no ASP.NET Core. Por exemplo, Kestrel usa a [configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration) para essa funcionalidade.

## <a name="grpc-interceptors-vs-middleware"></a>Interceptores gRPC vs middleware

ASP.NET Core [middleware](xref:fundamentals/middleware/index) oferece funcionalidades semelhantes em comparação com os interceptores em aplicativos gRPC baseados em C-Core. ASP.NET Core middleware e interceptores são conceitualmente semelhantes. Ambos:

* São usados para construir um pipeline que manipula uma solicitação gRPC.
* Permitir que o trabalho seja executado antes ou depois do próximo componente no pipeline.
* Forneça acesso a `HttpContext`:
  * No middleware, o `HttpContext` é um parâmetro.
  * Em interceptores, o `HttpContext` pode ser acessado usando o parâmetro `ServerCallContext` com o método de extensão `ServerCallContext.GetHttpContext`. Observe que esse recurso é específico dos interceptores em execução no ASP.NET Core.

diferenças de interceptador gRPC do middleware ASP.NET Core:

* Interceptores
  * Opere na camada gRPC de abstração usando o [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Fornecer acesso a:
    * A mensagem desserializada enviada a uma chamada.
    * A mensagem que está sendo retornada da chamada antes de ser serializada.
  * Pode detectar e tratar exceções lançadas dos serviços gRPCs.
* Middleware
  * É executado antes de interceptadores gRPC.
  * Opera nas mensagens HTTP/2 subjacentes.
  * Pode acessar somente os bytes dos fluxos de solicitação e resposta.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
