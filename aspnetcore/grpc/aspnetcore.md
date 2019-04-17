---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços de gRPC com o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: c99a499fad824c3ac026f6f390c826c0418fc069
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59515327"
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

### <a name="request-body-data-rate-limit"></a>Limite de taxa de dados de corpo de solicitação

Por padrão, o servidor Kestrel impõe uma [taxa de dados de corpo de solicitação mínimo](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>). Para o cliente de streaming e duplex, chamadas de streaming, essa taxa não pode ser atendida e a conexão pode ser esgotado. O mínimo limite de taxa de dados deve ser desabilitada quando o serviço gRPC inclui o cliente de streaming e duplex, chamadas de streaming do corpo da solicitação:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
