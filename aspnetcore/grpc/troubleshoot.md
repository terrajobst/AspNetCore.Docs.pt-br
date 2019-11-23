---
title: Solucionar problemas do gRPC no .NET Core
author: jamesnk
description: Solucionar erros ao usar o gRPC no .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519048"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="11802-103">Solucionar problemas do gRPC no .NET Core</span><span class="sxs-lookup"><span data-stu-id="11802-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="11802-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="11802-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="11802-105">Este documento aborda problemas comumente encontrados ao desenvolver aplicativos gRPC no .NET.</span><span class="sxs-lookup"><span data-stu-id="11802-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="11802-106">Incompatibilidade entre a configuração de SSL/TLS do cliente e do serviço</span><span class="sxs-lookup"><span data-stu-id="11802-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="11802-107">O modelo gRPC e os exemplos usam o protocolo TLS para proteger os serviços [do](https://tools.ietf.org/html/rfc5246) gRPC por padrão.</span><span class="sxs-lookup"><span data-stu-id="11802-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="11802-108">Os clientes gRPC precisam usar uma conexão segura para chamar os serviços gRPC protegidos com êxito.</span><span class="sxs-lookup"><span data-stu-id="11802-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="11802-109">Você pode verificar se o serviço gRPC do ASP.NET Core está usando o TLS nos logs gravados no início do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11802-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="11802-110">O serviço escutará em um ponto de extremidade HTTPS:</span><span class="sxs-lookup"><span data-stu-id="11802-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="11802-111">O cliente .NET Core deve usar `https` no endereço do servidor para fazer chamadas com uma conexão segura:</span><span class="sxs-lookup"><span data-stu-id="11802-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="11802-112">Todas as implementações de cliente do gRPC dão suporte a TLS.</span><span class="sxs-lookup"><span data-stu-id="11802-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="11802-113">Os clientes gRPC de outros idiomas normalmente exigem o canal configurado com `SslCredentials`.</span><span class="sxs-lookup"><span data-stu-id="11802-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="11802-114">`SslCredentials` especifica o certificado que o cliente usará e deve ser usado em vez de credenciais não seguras.</span><span class="sxs-lookup"><span data-stu-id="11802-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="11802-115">Para obter exemplos de como configurar as diferentes implementações de cliente gRPC para usar o TLS, consulte [autenticação do gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="11802-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="11802-116">Chamar um serviço gRPC com um certificado não confiável/inválido</span><span class="sxs-lookup"><span data-stu-id="11802-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="11802-117">O cliente .NET gRPC requer que o serviço tenha um certificado confiável.</span><span class="sxs-lookup"><span data-stu-id="11802-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="11802-118">A seguinte mensagem de erro é retornada ao chamar um serviço gRPC sem um certificado confiável:</span><span class="sxs-lookup"><span data-stu-id="11802-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="11802-119">Exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="11802-119">Unhandled exception.</span></span> <span data-ttu-id="11802-120">Sistema .net. http. HttpRequestexception: não foi possível estabelecer a conexão SSL, consulte a exceção interna.</span><span class="sxs-lookup"><span data-stu-id="11802-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="11802-121">---> System. Security. Authentication. Autenticaexception: o certificado remoto é inválido de acordo com o procedimento de validação.</span><span class="sxs-lookup"><span data-stu-id="11802-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="11802-122">Você poderá ver esse erro se estiver testando seu aplicativo localmente e o ASP.NET Core certificado de desenvolvimento HTTPS não for confiável.</span><span class="sxs-lookup"><span data-stu-id="11802-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="11802-123">Para obter instruções sobre como corrigir esse problema, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core no Windows e no macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="11802-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="11802-124">Se você estiver chamando um serviço gRPC em outro computador e não puder confiar no certificado, o cliente gRPC poderá ser configurado para ignorar o certificado inválido.</span><span class="sxs-lookup"><span data-stu-id="11802-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="11802-125">O código a seguir usa [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) para permitir chamadas sem um certificado confiável:</span><span class="sxs-lookup"><span data-stu-id="11802-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="11802-126">Certificados não confiáveis só devem ser usados durante o desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11802-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="11802-127">Os aplicativos de produção sempre devem usar certificados válidos.</span><span class="sxs-lookup"><span data-stu-id="11802-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="11802-128">Chamar serviços gRPCs inseguros com o cliente .NET Core</span><span class="sxs-lookup"><span data-stu-id="11802-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="11802-129">A configuração adicional é necessária para chamar serviços gRPCs inseguros com o cliente .NET Core.</span><span class="sxs-lookup"><span data-stu-id="11802-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="11802-130">O cliente gRPC deve definir a opção `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` para `true` e usar `http` no endereço do servidor:</span><span class="sxs-lookup"><span data-stu-id="11802-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="11802-131">Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS</span><span class="sxs-lookup"><span data-stu-id="11802-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="11802-132">O Kestrel não dá suporte a HTTP/2 com TLS no macOS e versões mais antigas do Windows, como o Windows 7.</span><span class="sxs-lookup"><span data-stu-id="11802-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="11802-133">O modelo ASP.NET Core gRPC e os exemplos usam TLS por padrão.</span><span class="sxs-lookup"><span data-stu-id="11802-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="11802-134">Você verá a seguinte mensagem de erro ao tentar iniciar o servidor gRPC:</span><span class="sxs-lookup"><span data-stu-id="11802-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="11802-135">Não é possível associar a https://localhost:5001 na interface de loopback IPv4: ' HTTP/2 sobre TLS não tem suporte no macOS devido ao suporte de ALPN ausente. '.</span><span class="sxs-lookup"><span data-stu-id="11802-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="11802-136">Para contornar esse problema, configure o Kestrel e o cliente gRPC para usar HTTP/2 *sem* TLS.</span><span class="sxs-lookup"><span data-stu-id="11802-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="11802-137">Você só deve fazer isso durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="11802-137">You should only do this during development.</span></span> <span data-ttu-id="11802-138">Não usar o TLS fará com que as mensagens gRPC sejam enviadas sem criptografia.</span><span class="sxs-lookup"><span data-stu-id="11802-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="11802-139">Kestrel deve configurar um ponto de extremidade HTTP/2 sem TLS em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="11802-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="11802-140">Quando um ponto de extremidade HTTP/2 é configurado sem TLS, o [ListenerOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deve ser definido como `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="11802-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="11802-141">`HttpProtocols.Http1AndHttp2` não pode ser usado porque o TLS é necessário para negociar HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="11802-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="11802-142">Sem o TLS, todas as conexões com o ponto de extremidade padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="11802-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="11802-143">O cliente gRPC também deve ser configurado para não usar o TLS.</span><span class="sxs-lookup"><span data-stu-id="11802-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="11802-144">Para obter mais informações, consulte [chamar serviços gRPCs inseguros com o cliente .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="11802-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="11802-145">O HTTP/2 sem TLS só deve ser usado durante o desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11802-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="11802-146">Os aplicativos de produção sempre devem usar a segurança de transporte.</span><span class="sxs-lookup"><span data-stu-id="11802-146">Production apps should always use transport security.</span></span> <span data-ttu-id="11802-147">Para obter mais informações, consulte [Security Considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="11802-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="11802-148">os C# ativos gRPC não são gerados pelo código de arquivos. proto</span><span class="sxs-lookup"><span data-stu-id="11802-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="11802-149">a geração de código gRPC de clientes concretos e classes base de serviço requer que os arquivos e as ferramentas do protobuf sejam referenciados de um projeto.</span><span class="sxs-lookup"><span data-stu-id="11802-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="11802-150">Você deve incluir:</span><span class="sxs-lookup"><span data-stu-id="11802-150">You must include:</span></span>

* <span data-ttu-id="11802-151">arquivos *. proto* que você deseja usar no `<Protobuf>` grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="11802-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="11802-152">[Arquivos *. proto* importados](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) devem ser referenciados pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="11802-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="11802-153">Referência de pacote para o pacote de ferramentas do gRPC [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="11802-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="11802-154">Para obter mais informações sobre como C# gerar ativos de gRPC, consulte <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="11802-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="11802-155">Por padrão, uma referência de `<Protobuf>` gera um cliente concreto e uma classe base de serviço.</span><span class="sxs-lookup"><span data-stu-id="11802-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="11802-156">O atributo de `GrpcServices` do elemento de referência pode ser usado C# para limitar a geração de ativos.</span><span class="sxs-lookup"><span data-stu-id="11802-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="11802-157">As opções de `GrpcServices` válidas são:</span><span class="sxs-lookup"><span data-stu-id="11802-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="11802-158">`Both` (padrão quando não presente)</span><span class="sxs-lookup"><span data-stu-id="11802-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="11802-159">Um ASP.NET Core aplicativo Web que hospeda serviços gRPCs precisa apenas da classe base de serviço gerada:</span><span class="sxs-lookup"><span data-stu-id="11802-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="11802-160">Um aplicativo cliente gRPC que faz chamadas gRPC precisa apenas do cliente concreto gerado:</span><span class="sxs-lookup"><span data-stu-id="11802-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="11802-161">Projetos do WPF não podem gerar C# ativos de gRPC de arquivos. proto</span><span class="sxs-lookup"><span data-stu-id="11802-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="11802-162">Os projetos do WPF têm um [problema conhecido](https://github.com/dotnet/wpf/issues/810) que impede a geração de código gRPC de funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="11802-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="11802-163">Quaisquer tipos de gRPC gerados em um projeto WPF referenciando `Grpc.Tools` e arquivos *. proto* criarão erros de compilação quando usados:</span><span class="sxs-lookup"><span data-stu-id="11802-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="11802-164">erro CS0246: não foi possível encontrar o nome do namespace ou tipo ' MyGrpcServices ' (está faltando uma diretiva using ou uma referência de assembly?)</span><span class="sxs-lookup"><span data-stu-id="11802-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="11802-165">Você pode solucionar esse problema:</span><span class="sxs-lookup"><span data-stu-id="11802-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="11802-166">Crie um novo projeto de biblioteca de classes do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="11802-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="11802-167">No novo projeto, adicione referências para habilitar [ C# a geração de código de arquivos *\*. proto* ](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="11802-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="11802-168">Adicione uma referência de pacote ao pacote [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="11802-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="11802-169">Adicione arquivos *\*. proto* ao grupo de itens de `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="11802-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="11802-170">No aplicativo do WPF, adicione uma referência ao novo projeto.</span><span class="sxs-lookup"><span data-stu-id="11802-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="11802-171">O aplicativo WPF pode usar os tipos gerados gRPC do novo projeto de biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="11802-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
