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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="5693e-103">Autenticação e autorização no gRPC para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5693e-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="5693e-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5693e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5693e-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5693e-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="5693e-106">Autenticar usuários chamando um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="5693e-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="5693e-107">gRPC pode ser usado com a [autenticação ASP.NET Core](xref:security/authentication/identity) para associar um usuário a cada chamada.</span><span class="sxs-lookup"><span data-stu-id="5693e-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="5693e-108">Veja a seguir um exemplo de `Startup.Configure` que usa a autenticação gRPC e ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5693e-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="5693e-109">A ordem na qual você registra o ASP.NET Core de middleware de autenticação é importante.</span><span class="sxs-lookup"><span data-stu-id="5693e-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="5693e-110">Sempre chamar `UseAuthentication` e `UseAuthorization` depois `UseRouting` de e `UseEndpoints`antes.</span><span class="sxs-lookup"><span data-stu-id="5693e-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="5693e-111">Depois que a autenticação tiver sido configurada, o usuário poderá ser acessado em `ServerCallContext`um método de serviço gRPC por meio do.</span><span class="sxs-lookup"><span data-stu-id="5693e-111">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="5693e-112">Autenticação de token de portador</span><span class="sxs-lookup"><span data-stu-id="5693e-112">Bearer token authentication</span></span>

<span data-ttu-id="5693e-113">O cliente pode fornecer um token de acesso para autenticação.</span><span class="sxs-lookup"><span data-stu-id="5693e-113">The client can provide an access token for authentication.</span></span> <span data-ttu-id="5693e-114">O servidor valida o token e o usa para identificar o usuário.</span><span class="sxs-lookup"><span data-stu-id="5693e-114">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="5693e-115">No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="5693e-115">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="5693e-116">No cliente .NET gRPC, o token pode ser enviado com chamadas como um cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="5693e-116">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="5693e-117">Autenticação de certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="5693e-117">Client certificate authentication</span></span>

<span data-ttu-id="5693e-118">Um cliente pode, opcionalmente, fornecer um certificado de cliente para autenticação.</span><span class="sxs-lookup"><span data-stu-id="5693e-118">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="5693e-119">A [autenticação de certificado](https://tools.ietf.org/html/rfc5246#section-7.4.4) ocorre no nível de TLS, muito antes que ele chegue a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5693e-119">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="5693e-120">Quando a solicitação entra ASP.NET Core, o [pacote de autenticação de certificado de cliente](xref:security/authentication/certauth) permite que você resolva o `ClaimsPrincipal`certificado para um.</span><span class="sxs-lookup"><span data-stu-id="5693e-120">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="5693e-121">O host precisa ser configurado para aceitar certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="5693e-121">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="5693e-122">Consulte [Configurar o host para exigir certificados](xref:security/authentication/certauth#configure-your-host-to-require-certificates) para obter informações sobre como aceitar certificados de cliente no Kestrel, no IIS e no Azure.</span><span class="sxs-lookup"><span data-stu-id="5693e-122">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="5693e-123">No cliente .net gRPC, o certificado do cliente é adicionado a `HttpClientHandler` que é usado para criar o cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="5693e-123">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="5693e-124">Outros mecanismos de autenticação</span><span class="sxs-lookup"><span data-stu-id="5693e-124">Other authentication mechanisms</span></span>

<span data-ttu-id="5693e-125">Além do token de portador e da autenticação de certificado do cliente, todos os ASP.NET Core mecanismos de autenticação com suporte, como OAuth, OpenID e Negotiate devem funcionar com gRPC.</span><span class="sxs-lookup"><span data-stu-id="5693e-125">In addition to bearer token and client certificate authentication, all ASP.NET Core supported authentication mechanisms such as OAuth, OpenID and Negotiate should work with gRPC.</span></span> <span data-ttu-id="5693e-126">Visite [ASP.NET Core autenticação](xref:security/authentication/identity) para obter mais informações sobre como configurar a autenticação no lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="5693e-126">Visit [ASP.NET Core authentication](xref:security/authentication/identity) for more information for configuring authentication on the server side.</span></span>

<span data-ttu-id="5693e-127">A configuração do lado do cliente dependerá do mecanismo de autenticação que você está usando.</span><span class="sxs-lookup"><span data-stu-id="5693e-127">Client side configuration will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="5693e-128">Os exemplos anteriores de token de portador e autenticação de certificado de cliente mostram duas maneiras que o cliente gRPC pode ser configurado para enviar metadados de autenticação com chamadas gRPC:</span><span class="sxs-lookup"><span data-stu-id="5693e-128">The previous bearer token and client certificate authentication examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="5693e-129">Clientes gRPC fortemente tipados `HttpClient` usam internamente.</span><span class="sxs-lookup"><span data-stu-id="5693e-129">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="5693e-130">A autenticação pode ser configurada em [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)ou adicionando instâncias personalizadas [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) ao `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="5693e-130">Authentication can be configured on [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="5693e-131">Cada chamada gRPC tem um argumento `CallOptions` opcional.</span><span class="sxs-lookup"><span data-stu-id="5693e-131">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="5693e-132">Cabeçalhos personalizados podem ser enviados usando a coleção de cabeçalhos da opção.</span><span class="sxs-lookup"><span data-stu-id="5693e-132">Custom headers can be sent using the option's headers collection.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="5693e-133">Autorizar usuários a acessar os serviços e métodos de serviço</span><span class="sxs-lookup"><span data-stu-id="5693e-133">Authorize users to access services and service methods</span></span>

<span data-ttu-id="5693e-134">Por padrão, todos os métodos em um serviço podem ser chamados por usuários não autenticados.</span><span class="sxs-lookup"><span data-stu-id="5693e-134">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="5693e-135">Para exigir autenticação, aplique o atributo [[autorizar]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ao serviço:</span><span class="sxs-lookup"><span data-stu-id="5693e-135">To require authentication, apply the [[Authorize]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="5693e-136">Você pode usar os argumentos do construtor e as propriedades `[Authorize]` do atributo para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas.</span><span class="sxs-lookup"><span data-stu-id="5693e-136">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="5693e-137">Por exemplo, se você tiver uma política de autorização personalizada `MyAuthorizationPolicy`chamada, certifique-se de que somente os usuários que correspondem a essa política possam acessar o serviço usando o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5693e-137">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="5693e-138">Os métodos de serviço individuais também `[Authorize]` podem ter o atributo aplicado.</span><span class="sxs-lookup"><span data-stu-id="5693e-138">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="5693e-139">Se o usuário atual não corresponder às políticas **aplicadas ao método** e à classe, um erro será retornado para o chamador:</span><span class="sxs-lookup"><span data-stu-id="5693e-139">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5693e-140">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5693e-140">Additional resources</span></span>

* [<span data-ttu-id="5693e-141">Autenticação de token de portador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5693e-141">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="5693e-142">Configurar a autenticação de certificado de cliente no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5693e-142">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
