---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 28e6b8589bbe0b6a3723b64736c723c883302571
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238168"
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

Em *Startup.cs*:

* gRPC é habilitado com o `AddGrpc` método.
* Cada serviço gRPC é adicionado ao pipeline de roteamento por meio `MapGrpcService` do método.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais. Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.

### <a name="configure-kestrel"></a>Configurar o Kestrel

Kestrel pontos de extremidade gRPC:

* Exigir HTTP/2.
* Deve ser protegido com HTTPS.

#### <a name="http2"></a>HTTP/2

gRPC requer HTTP/2. gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) é `HTTP/2`.

O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos. Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.

#### <a name="https"></a>HTTPS

Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com HTTPS. No desenvolvimento, um ponto de extremidade HTTPS é criado `https://localhost:5001` automaticamente quando o certificado de desenvolvimento de ASP.NET Core está presente. Nenhuma configuração é necessária.

Em produção, HTTPS precisa ser configurado explicitamente. No exemplo de *appSettings. JSON* a seguir, um ponto de extremidade http/2 protegido com HTTPS é fornecido:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http2"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // This endpoint will use HTTP/2 and HTTPS on port 5001.
                options.Listen(IPAddress.Any, 5001, listenOptions =>
                {
                    listenOptions.Protocols = HttpProtocols.Http2;
                    listenOptions.UseHttps("<path to .pfx file>", 
                        "<certificate password>");
                });
            });
            webBuilder.UseStartup<Startup>();
        });
```

Quando um ponto de extremidade HTTP/2 é configurado sem HTTPS, o [ListenerOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deve ser `HttpProtocols.Http2`definido como. `HttpProtocols.Http1AndHttp2`Não pode ser usado porque HTTPS é necessário para negociar HTTP/2. Sem HTTPS, todas as conexões com o ponto de extremidade padrão para HTTP/1.1 e chamadas gRPC falham.

Para obter mais informações sobre como habilitar HTTP/2 e HTTPS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> o macOS não dá suporte a ASP.NET Core gRPC com [segurança de camada de transporte (TLS)](https://tools.ietf.org/html/rfc5246). É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS. Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

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
* <xref:fundamentals/servers/kestrel>
