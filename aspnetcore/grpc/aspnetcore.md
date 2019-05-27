---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços de gRPC com o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 1f019fac23982a95fa37d43099522f4b3e9d107a
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66039279"
---
# <a name="grpc-services-with-aspnet-core"></a>Serviços do gRPC com o ASP.NET Core

Este documento mostra como começar com os serviços de gRPC usando o ASP.NET Core.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Introdução ao serviço de gRPC no ASP.NET Core

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Ver [Introdução aos serviços de gRPC](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Adicionar serviços de gRPC para um aplicativo ASP.NET Core

gRPC requer os seguintes pacotes:

* [Grpc.AspNetCore.Server](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) para protobuf APIs da mensagem.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a>Configurar gRPC

gRPC é habilitada com o `AddGrpc` método:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=5)]

Cada serviço gRPC é adicionado ao pipeline de roteamento por meio de `MapGrpcService` método:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=21)]

O pipeline de roteamento de compartilhamento de recursos e o middleware do ASP.NET Core, portanto, um aplicativo pode ser configurado para servir os manipuladores de solicitação adicionais. Os manipuladores de solicitação adicionais, como controladores MVC, trabalhassem em paralelo com os serviços configurados gRPC.

## <a name="integration-with-aspnet-core-apis"></a>Integração com as APIs do ASP.NET Core

gRPC serviços têm acesso completo aos recursos do ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (DI) e [log](xref:fundamentals/logging/index). Por exemplo, a implementação do serviço pode resolver um serviço de agente de log do contêiner de DI por meio do construtor:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (Singleton, escopo ou transitório).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Resolver HttpContext em gRPC métodos

A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, host, cabeçalho e trailers. O acesso é por meio de `ServerCallContext` argumento passado para cada método gRPC:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.NET. O `GetHttpContext` método de extensão fornece acesso completo para o `HttpContext` que representa a mensagem HTTP/2 subjacente nas APIs do ASP.NET:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet1)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
