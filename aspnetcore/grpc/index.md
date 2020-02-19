---
title: Introdução ao gRPC no .NET Core
author: juntaoluo
description: Saiba mais sobre os serviços de gRPC com a pilha do ASP.NET Core e o servidor Kestrel.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77446951"
---
# <a name="introduction-to-grpc-on-net-core"></a>Introdução ao gRPC no .NET Core

Por [John Luo](https://github.com/juntaoluo) e [James Newton – King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) é uma estrutura de RPC (Chamada de Procedimento Remoto) de linguagem independente de alto desempenho.

Os principais benefícios de gRPC são:
* Estrutura RPC leve, de alto desempenho e moderna.
* Desenvolvimento da API de primeiro contrato, usando buffers de protocolo, por padrão, permitindo implementações independente de linguagem.
* As ferramentas disponíveis para várias linguagens gerarem clientes e servidores fortemente tipados.
* Dá suporte ao cliente, servidor e chamadas bi-direcionais de streaming.
* Uso de rede reduzida com a serialização binária Protobuf.

Esses benefícios tornam o gRPC ideal para:
* Microsserviços leves em que a eficiência é crítica.
* Sistemas poliglotas nos quais múltiplas linguagens são necessárias para o desenvolvimento.
* Serviços ponto a ponto em tempo real que precisam lidar com solicitações ou respostas de streaming.

## <a name="c-tooling-support-for-proto-files"></a>C#Suporte de ferramentas para arquivos. proto

o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API. Os serviços e as mensagens são definidos nos arquivos *\*. proto* :

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Os tipos .NET para serviços, clientes e mensagens são gerados automaticamente incluindo *\*arquivos. proto* em um projeto:

* Adicione uma referência de pacote ao pacote [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .
* Adicione arquivos *\*. proto* ao grupo de itens de `<Protobuf>`.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Para obter mais informações sobre o suporte de ferramentas do gRPC, consulte <xref:grpc/basics>.

## <a name="grpc-services-on-aspnet-core"></a>serviços gRPCs no ASP.NET Core

os serviços gRPCs podem ser hospedados em ASP.NET Core. Os serviços têm integração total com recursos populares de ASP.NET Core, como registro em log, injeção de dependência (DI), autenticação e autorização.

O modelo de projeto de serviço gRPC fornece um serviço inicial:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService` herda do tipo `GreeterBase`, que é gerado a partir do serviço de `Greeter` no arquivo *\*. proto* . O serviço torna-se acessível para clientes no *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Para saber mais sobre os serviços gRPCs no ASP.NET Core, consulte <xref:grpc/aspnetcore>.

## <a name="call-grpc-services-with-a-net-client"></a>Chamar serviços gRPCs com um cliente .NET

Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de arquivos *\*. proto* ](xref:grpc/basics#generated-c-assets). O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no arquivo *\*. proto* .

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Um cliente gRPC é criado usando um canal, que representa uma conexão de vida longa para um serviço gRPC. Um canal pode ser criado usando `GrpcChannel.ForAddress`.

Para obter mais informações sobre como criar clientes e chamar métodos de serviço diferentes, consulte <xref:grpc/client>.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
