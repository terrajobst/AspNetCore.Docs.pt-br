---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 38111c152c581c50767f9cd4e5fa257bd3fd804e
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022309"
---
# <a name="grpc-services-with-aspnet-core"></a>Serviços do gRPC com o ASP.NET Core

Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Introdução ao serviço de gRPC no ASP.NET Core

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Adicionar serviços gRPCs a um aplicativo ASP.NET Core

gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .

### <a name="configure-grpc"></a>Configurar o gRPC

gRPC é habilitado com o `AddGrpc` método:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

Cada serviço gRPC é adicionado ao pipeline de roteamento por meio `MapGrpcService` do método:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais. Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.

## <a name="integration-with-aspnet-core-apis"></a>Integração com APIs de ASP.NET Core

os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index). Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Resolver HttpContext em métodos gRPC

A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers. O acesso é por `ServerCallContext` meio do argumento passado para cada método gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.net. O `GetHttpContext` método de extensão fornece acesso completo `HttpContext` à representação da mensagem http/2 subjacente em APIs ASP.net:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
