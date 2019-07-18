---
title: Autenticação e autorização no gRPC para ASP.NET Core
author: jamesnk
description: Saiba como usar a autenticação e a autorização no gRPC para ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/07/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 49024295e4db7976924397bb24567d92d6298562
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308769"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Autenticação e autorização no gRPC para ASP.NET Core

Por [James Newton – King](https://twitter.com/jamesnk)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Autenticar usuários chamando um serviço gRPC

gRPC pode ser usado com a [autenticação ASP.NET Core](xref:security/authentication/identity) para associar um usuário a cada chamada.

Veja a seguir um exemplo de `Startup.Configure` que usa a autenticação gRPC e ASP.NET Core:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> A ordem na qual você registra o ASP.NET Core de middleware de autenticação é importante. Sempre chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` de e `UseEndpoints`antes.

Depois que a autenticação tiver sido configurada, o usuário poderá ser acessado em `ServerCallContext`um método de serviço gRPC por meio do.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Autenticação de token de portador

O cliente pode fornecer um token de acesso para autenticação. O servidor valida o token e o usa para identificar o usuário.

No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

No cliente .NET gRPC, o token pode ser enviado com chamadas como um cabeçalho:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

### <a name="client-certificate-authentication"></a>Autenticação de certificado de cliente

Um cliente pode, opcionalmente, fornecer um certificado de cliente para autenticação. A [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core. Quando a solicitação entra ASP.NET Core, o [pacote de autenticação de certificado de cliente](xref:security/authentication/certauth) permite que você resolva o `ClaimsPrincipal`certificado para um.

> [!NOTE]
> O host precisa ser configurado para aceitar certificados de cliente. Consulte [Configurar o host para exigir certificados](xref:security/authentication/certauth#configure-your-host-to-require-certificates) para obter informações sobre como aceitar certificados de cliente no Kestrel, no IIS e no Azure.

No cliente .net gRPC, o certificado do cliente é adicionado a `HttpClientHandler` que é usado para criar o cliente gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC client
    var httpClient = new HttpClient(handler);
    httpClient.BaseAddress = new Uri(baseAddress);

    return GrpcClient.Create<Ticketer.TicketerClient>(httpClient);
}
```

### <a name="other-authentication-mechanisms"></a>Outros mecanismos de autenticação

Além do token de portador e da autenticação de certificado do cliente, todos os ASP.NET Core mecanismos de autenticação com suporte, como OAuth, OpenID e Negotiate devem funcionar com gRPC. Visite [ASP.NET Core autenticação](xref:security/authentication/identity) para obter mais informações sobre como configurar a autenticação no lado do servidor.

A configuração do lado do cliente dependerá do mecanismo de autenticação que você está usando. Os exemplos anteriores de token de portador e autenticação de certificado de cliente mostram duas maneiras que o cliente gRPC pode ser configurado para enviar metadados de autenticação com chamadas gRPC:

* Clientes gRPC fortemente tipados `HttpClient` usam internamente. A autenticação pode ser configurada em [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)ou adicionando instâncias personalizadas [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) ao `HttpClient`.
* Cada chamada gRPC tem um argumento `CallOptions` opcional. Cabeçalhos personalizados podem ser enviados usando a coleção de cabeçalhos da opção.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizar usuários a acessar os serviços e métodos de serviço

Por padrão, todos os métodos em um serviço podem ser chamados por usuários não autenticados. Para exigir autenticação, aplique o atributo [[autorizar]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ao serviço:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Você pode usar os argumentos do construtor e as propriedades `[Authorize]` do atributo para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas. Por exemplo, se você tiver uma política de autorização personalizada `MyAuthorizationPolicy`chamada, certifique-se de que somente os usuários que correspondem a essa política possam acessar o serviço usando o seguinte código:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Os métodos de serviço individuais também `[Authorize]` podem ter o atributo aplicado. Se o usuário atual não corresponder às políticas **aplicadas ao método** e à classe, um erro será retornado para o chamador:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Recursos adicionais

* [Autenticação de token de portador no ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configurar a autenticação de certificado de cliente no ASP.NET Core](xref:security/authentication/certauth)
