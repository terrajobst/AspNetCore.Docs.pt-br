---
title: Solucionar problemas do gRPC no .NET Core
author: jamesnk
description: Solucionar erros ao usar o gRPC no .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/17/2019
uid: grpc/troubleshoot
ms.openlocfilehash: 7621266dfe26b7126d1607e195dd5dcaab4efa55
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886486"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="ad462-103">Solucionar problemas do gRPC no .NET Core</span><span class="sxs-lookup"><span data-stu-id="ad462-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="ad462-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ad462-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="ad462-105">Incompatibilidade entre a configuração de SSL/TLS do cliente e do serviço</span><span class="sxs-lookup"><span data-stu-id="ad462-105">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="ad462-106">O modelo gRPC e os exemplos usam o protocolo TLS para proteger os serviços [do](https://tools.ietf.org/html/rfc5246) gRPC por padrão.</span><span class="sxs-lookup"><span data-stu-id="ad462-106">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="ad462-107">Os clientes gRPC precisam usar uma conexão segura para chamar os serviços gRPC protegidos com êxito.</span><span class="sxs-lookup"><span data-stu-id="ad462-107">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="ad462-108">Você pode verificar se o serviço gRPC do ASP.NET Core está usando o TLS nos logs gravados no início do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ad462-108">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="ad462-109">O serviço escutará em um ponto de extremidade HTTPS:</span><span class="sxs-lookup"><span data-stu-id="ad462-109">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="ad462-110">O cliente .NET Core deve usar `https` no endereço do servidor para fazer chamadas com uma conexão segura:</span><span class="sxs-lookup"><span data-stu-id="ad462-110">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="ad462-111">Todas as implementações de cliente do gRPC dão suporte a TLS.</span><span class="sxs-lookup"><span data-stu-id="ad462-111">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="ad462-112">Os clientes gRPC de outras linguagens normalmente exigem o canal `SslCredentials`configurado com o.</span><span class="sxs-lookup"><span data-stu-id="ad462-112">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="ad462-113">`SslCredentials`Especifica o certificado que o cliente usará e deve ser usado em vez de credenciais não seguras.</span><span class="sxs-lookup"><span data-stu-id="ad462-113">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="ad462-114">Para obter exemplos de como configurar as diferentes implementações de cliente gRPC para usar o TLS, consulte [autenticação do gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="ad462-114">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="ad462-115">Chamar serviços gRPCs inseguros com o cliente .NET Core</span><span class="sxs-lookup"><span data-stu-id="ad462-115">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="ad462-116">A configuração adicional é necessária para chamar serviços gRPCs inseguros com o cliente .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ad462-116">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="ad462-117">O cliente gRPC deve definir o `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` comutador `true` para e `http` usar no endereço do servidor:</span><span class="sxs-lookup"><span data-stu-id="ad462-117">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="ad462-118">Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS</span><span class="sxs-lookup"><span data-stu-id="ad462-118">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="ad462-119">O Kestrel não dá suporte a HTTP/2 com TLS no macOS e versões mais antigas do Windows, como o Windows 7.</span><span class="sxs-lookup"><span data-stu-id="ad462-119">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="ad462-120">O modelo ASP.NET Core gRPC e os exemplos usam TLS por padrão.</span><span class="sxs-lookup"><span data-stu-id="ad462-120">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="ad462-121">Você verá a seguinte mensagem de erro ao tentar iniciar o servidor gRPC:</span><span class="sxs-lookup"><span data-stu-id="ad462-121">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="ad462-122">Não é possível associar https://localhost:5001 ao na interface de loopback IPv4: Não há suporte para ' HTTP/2 por TLS no macOS devido a um suporte ALPN ausente. '.</span><span class="sxs-lookup"><span data-stu-id="ad462-122">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="ad462-123">Para contornar esse problema, configure o Kestrel e o cliente gRPC para usar HTTP/2 *sem* TLS.</span><span class="sxs-lookup"><span data-stu-id="ad462-123">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="ad462-124">Você só deve fazer isso durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ad462-124">You should only do this during development.</span></span> <span data-ttu-id="ad462-125">Não usar o TLS fará com que as mensagens gRPC sejam enviadas sem criptografia.</span><span class="sxs-lookup"><span data-stu-id="ad462-125">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="ad462-126">Kestrel deve configurar um ponto de extremidade HTTP/2 sem TLS em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ad462-126">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="ad462-127">Quando um ponto de extremidade HTTP/2 é configurado sem TLS, o [ListenerOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deve ser `HttpProtocols.Http2`definido como.</span><span class="sxs-lookup"><span data-stu-id="ad462-127">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="ad462-128">`HttpProtocols.Http1AndHttp2`Não pode ser usado porque o TLS é necessário para negociar HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ad462-128">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="ad462-129">Sem o TLS, todas as conexões com o ponto de extremidade padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="ad462-129">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="ad462-130">O cliente gRPC também deve ser configurado para não usar o TLS.</span><span class="sxs-lookup"><span data-stu-id="ad462-130">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="ad462-131">Para obter mais informações, consulte [chamar serviços gRPCs inseguros com o cliente .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="ad462-131">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="ad462-132">O HTTP/2 sem TLS só deve ser usado durante o desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ad462-132">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="ad462-133">Os aplicativos de produção sempre devem usar a segurança de transporte.</span><span class="sxs-lookup"><span data-stu-id="ad462-133">Production apps should always use transport security.</span></span> <span data-ttu-id="ad462-134">Para obter mais informações, consulte [Security Considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="ad462-134">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="ad462-135">os C# ativos gRPC não são gerados pelo código de  *\*arquivos. proto*</span><span class="sxs-lookup"><span data-stu-id="ad462-135">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="ad462-136">a geração de código gRPC de clientes concretos e classes base de serviço requer que os arquivos e as ferramentas do protobuf sejam referenciados de um projeto.</span><span class="sxs-lookup"><span data-stu-id="ad462-136">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="ad462-137">Você deve incluir:</span><span class="sxs-lookup"><span data-stu-id="ad462-137">You must include:</span></span>

* <span data-ttu-id="ad462-138">arquivos *. proto* que você deseja usar no grupo `<Protobuf>` de itens.</span><span class="sxs-lookup"><span data-stu-id="ad462-138">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="ad462-139">[Arquivos *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) importados devem ser referenciados pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="ad462-139">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="ad462-140">Referência de pacote para o pacote de ferramentas do gRPC [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="ad462-140">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="ad462-141">Para obter mais informações sobre como C# gerar ativos de <xref:grpc/basics>gRPC, consulte.</span><span class="sxs-lookup"><span data-stu-id="ad462-141">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="ad462-142">Por padrão, uma `<Protobuf>` referência gera um cliente concreto e uma classe base de serviço.</span><span class="sxs-lookup"><span data-stu-id="ad462-142">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="ad462-143">O atributo do `GrpcServices` elemento de referência pode ser usado para C# limitar a geração de ativos.</span><span class="sxs-lookup"><span data-stu-id="ad462-143">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="ad462-144">As `GrpcServices` opções válidas são:</span><span class="sxs-lookup"><span data-stu-id="ad462-144">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="ad462-145">`Both`(padrão quando não presente)</span><span class="sxs-lookup"><span data-stu-id="ad462-145">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="ad462-146">Um ASP.NET Core aplicativo Web que hospeda serviços gRPCs precisa apenas da classe base de serviço gerada:</span><span class="sxs-lookup"><span data-stu-id="ad462-146">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="ad462-147">Um aplicativo cliente gRPC que faz chamadas gRPC precisa apenas do cliente concreto gerado:</span><span class="sxs-lookup"><span data-stu-id="ad462-147">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
