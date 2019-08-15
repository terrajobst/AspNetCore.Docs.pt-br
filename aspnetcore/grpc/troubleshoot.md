---
title: Solucionar problemas do gRPC no .NET Core
author: jamesnk
description: Solucionar erros ao usar o gRPC no .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/12/2019
uid: grpc/troubleshoot
ms.openlocfilehash: ad74bfa57d2dde316734d55d86075f463e78ee56
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029029"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Solucionar problemas do gRPC no .NET Core

Por [James Newton – King](https://twitter.com/jamesnk)

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Incompatibilidade entre a configuração de SSL/TLS do cliente e do serviço

O modelo gRPC e os exemplos usam o protocolo TLS para proteger os serviços [do](https://tools.ietf.org/html/rfc5246) gRPC por padrão. Os clientes gRPC precisam usar uma conexão segura para chamar os serviços gRPC protegidos com êxito.

Você pode verificar se o serviço gRPC do ASP.NET Core está usando o TLS nos logs gravados no início do aplicativo. O serviço escutará em um ponto de extremidade HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

O cliente .NET Core deve usar `https` no endereço do servidor para fazer chamadas com uma conexão segura:

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

Todas as implementações de cliente do gRPC dão suporte a TLS. Os clientes gRPC de outras linguagens normalmente exigem o canal `SslCredentials`configurado com o. `SslCredentials`Especifica o certificado que o cliente usará e deve ser usado em vez de credenciais não seguras. Para obter exemplos de como configurar as diferentes implementações de cliente gRPC para usar o TLS, consulte [autenticação do gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Chamar serviços gRPCs inseguros com o cliente .NET Core

A configuração adicional é necessária para chamar serviços gRPCs inseguros com o cliente .NET Core. O cliente gRPC deve definir o `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` comutador `true` para e `http` usar no endereço do servidor:

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS

O Kestrel não dá suporte a HTTP/2 com TLS no macOS e versões mais antigas do Windows, como o Windows 7. O modelo ASP.NET Core gRPC e os exemplos usam TLS por padrão. Você verá a seguinte mensagem de erro ao tentar iniciar o servidor gRPC:

> Não é possível associar https://localhost:5001 ao na interface de loopback IPv4: Não há suporte para ' HTTP/2 por TLS no macOS devido a um suporte ALPN ausente. '.

Para contornar esse problema, configure o Kestrel e o cliente gRPC para usar HTTP/2 *sem* TLS. Você só deve fazer isso durante o desenvolvimento. Não usar o TLS fará com que as mensagens gRPC sejam enviadas sem criptografia.

Kestrel deve configurar um ponto de extremidade HTTP/2 sem TLS em *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

O cliente gRPC também deve ser configurado para não usar o TLS. Para obter mais informações, consulte [chamar serviços gRPCs inseguros com o cliente .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> O HTTP/2 sem TLS só deve ser usado durante o desenvolvimento do aplicativo. Os aplicativos de produção sempre devem usar a segurança de transporte. Para obter mais informações, consulte [Security Considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>os C# ativos gRPC não são gerados pelo código de  *\*arquivos. proto*

a geração de código gRPC de clientes concretos e classes base de serviço requer que os arquivos e as ferramentas do protobuf sejam referenciados de um projeto. Você deve incluir:

* arquivos *. proto* que você deseja usar no grupo `<Protobuf>` de itens. [Arquivos *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) importados devem ser referenciados pelo projeto.
* Referência de pacote para o pacote de ferramentas do gRPC [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).

Para obter mais informações sobre como C# gerar ativos de <xref:grpc/basics>gRPC, consulte.

Por padrão, uma `<Protobuf>` referência gera um cliente concreto e uma classe base de serviço. O atributo do `GrpcServices` elemento de referência pode ser usado para C# limitar a geração de ativos. As `GrpcServices` opções válidas são:

* `Both`(padrão quando não presente)
* `Server`
* `Client`
* `None`

Um ASP.NET Core aplicativo Web que hospeda serviços gRPCs precisa apenas da classe base de serviço gerada:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Um aplicativo cliente gRPC que faz chamadas gRPC precisa apenas do cliente concreto gerado:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
