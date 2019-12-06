---
title: Autenticação e autorização no ASP.NET Core SignalR
author: bradygaster
description: Saiba como usar a autenticação e a autorização no ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 091cc9b2adc1f6a8fac79519884695d1c1725d2a
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880412"
---
# <a name="authentication-and-authorization-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="e78f5-103">Autenticação e autorização no ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="e78f5-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="e78f5-104">Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e78f5-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="e78f5-105">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e78f5-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-opno-locsignalr-hub"></a><span data-ttu-id="e78f5-106">Autenticar usuários que se conectam a um hub de SignalR</span><span class="sxs-lookup"><span data-stu-id="e78f5-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="e78f5-107"> pode ser usado com a [autenticação do ASP.NET Core](xref:security/authentication/identity) para associar um usuário a cada conexão.</span><span class="sxs-lookup"><span data-stu-id="e78f5-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="e78f5-108">Em um Hub, os dados de autenticação podem ser acessados da propriedade [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="e78f5-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="e78f5-109">A autenticação permite que o Hub Chame métodos em todas as conexões associadas a um usuário.</span><span class="sxs-lookup"><span data-stu-id="e78f5-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="e78f5-110">Para obter mais informações, consulte [gerenciar usuários e grupos no SignalR](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="e78f5-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="e78f5-111">Várias conexões podem ser associadas a um único usuário.</span><span class="sxs-lookup"><span data-stu-id="e78f5-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="e78f5-112">Veja a seguir um exemplo de `Startup.Configure` que usa SignalR e ASP.NET Core autenticação:</span><span class="sxs-lookup"><span data-stu-id="e78f5-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="e78f5-113">A ordem na qual você registra o SignalR e o middleware de autenticação ASP.NET Core é importante.</span><span class="sxs-lookup"><span data-stu-id="e78f5-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="e78f5-114">Sempre chame `UseAuthentication` antes de `UseSignalR` para que SignalR tenha um usuário no `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="e78f5-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="e78f5-115">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="e78f5-115">Cookie authentication</span></span>

<span data-ttu-id="e78f5-116">Em um aplicativo baseado em navegador, a autenticação de cookie permite que suas credenciais de usuário existentes fluam automaticamente para SignalR conexões.</span><span class="sxs-lookup"><span data-stu-id="e78f5-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="e78f5-117">Ao usar o cliente de navegador, nenhuma configuração adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="e78f5-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="e78f5-118">Se o usuário estiver conectado ao seu aplicativo, a conexão SignalR herdará automaticamente essa autenticação.</span><span class="sxs-lookup"><span data-stu-id="e78f5-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="e78f5-119">Cookies são uma maneira específica do navegador de enviar tokens de acesso, mas os clientes sem navegador podem enviá-los.</span><span class="sxs-lookup"><span data-stu-id="e78f5-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="e78f5-120">Ao usar o [cliente .net](xref:signalr/dotnet-client), a propriedade `Cookies` pode ser configurada na chamada `.WithUrl` para fornecer um cookie.</span><span class="sxs-lookup"><span data-stu-id="e78f5-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="e78f5-121">No entanto, usar a autenticação de cookie do cliente .NET requer que o aplicativo forneça uma API para trocar dados de autenticação para um cookie.</span><span class="sxs-lookup"><span data-stu-id="e78f5-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="e78f5-122">Autenticação de token de portador</span><span class="sxs-lookup"><span data-stu-id="e78f5-122">Bearer token authentication</span></span>

<span data-ttu-id="e78f5-123">O cliente pode fornecer um token de acesso em vez de usar um cookie.</span><span class="sxs-lookup"><span data-stu-id="e78f5-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="e78f5-124">O servidor valida o token e o usa para identificar o usuário.</span><span class="sxs-lookup"><span data-stu-id="e78f5-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="e78f5-125">Essa validação é feita somente quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="e78f5-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="e78f5-126">Durante a vida útil da conexão, o servidor não é revalidado automaticamente para verificar a revogação de tokens.</span><span class="sxs-lookup"><span data-stu-id="e78f5-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="e78f5-127">No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="e78f5-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="e78f5-128">No cliente JavaScript, o token pode ser fornecido usando a opção [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="e78f5-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="e78f5-129">No cliente .NET, há uma propriedade [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) semelhante que pode ser usada para configurar o token:</span><span class="sxs-lookup"><span data-stu-id="e78f5-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="e78f5-130">A função de token de acesso que você fornece é chamada antes de **cada** solicitação HTTP feita por SignalR.</span><span class="sxs-lookup"><span data-stu-id="e78f5-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="e78f5-131">Se você precisar renovar o token para manter a conexão ativa (porque ela pode expirar durante a conexão), faça isso de dentro dessa função e retorne o token atualizado.</span><span class="sxs-lookup"><span data-stu-id="e78f5-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="e78f5-132">Nas APIs Web padrão, os tokens de portador são enviados em um cabeçalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="e78f5-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="e78f5-133">No entanto, SignalR não pode definir esses cabeçalhos em navegadores ao usar alguns transportes.</span><span class="sxs-lookup"><span data-stu-id="e78f5-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="e78f5-134">Ao usar Websockets e eventos enviados pelo servidor, o token é transmitido como um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="e78f5-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="e78f5-135">Para dar suporte a isso no servidor, é necessária uma configuração adicional:</span><span class="sxs-lookup"><span data-stu-id="e78f5-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="e78f5-136">A cadeia de caracteres de consulta é usada em navegadores ao se conectar com WebSockets e eventos enviados pelo servidor devido a limitações da API do navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f5-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="e78f5-137">Ao usar HTTPS, os valores de cadeia de caracteres de consulta são protegidos pela conexão TLS.</span><span class="sxs-lookup"><span data-stu-id="e78f5-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="e78f5-138">No entanto, muitos servidores registram valores de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="e78f5-138">However, many servers log query string values.</span></span> <span data-ttu-id="e78f5-139">Para obter mais informações, consulte [Security Considerations in ASP.NET Core SignalR](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="e78f5-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="e78f5-140"> usa cabeçalhos para transmitir tokens em ambientes que dão suporte a eles (como os clientes .NET e Java).</span><span class="sxs-lookup"><span data-stu-id="e78f5-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="e78f5-141">Cookies versus tokens de portador</span><span class="sxs-lookup"><span data-stu-id="e78f5-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="e78f5-142">Os cookies são específicos para os navegadores.</span><span class="sxs-lookup"><span data-stu-id="e78f5-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="e78f5-143">Enviá-los de outros tipos de clientes adiciona complexidade comparada ao envio de tokens de portador.</span><span class="sxs-lookup"><span data-stu-id="e78f5-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="e78f5-144">Consequentemente, a autenticação de cookie não é recomendada, a menos que o aplicativo precise apenas autenticar usuários do cliente de navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f5-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="e78f5-145">A autenticação de token de portador é a abordagem recomendada ao usar clientes diferentes do cliente de navegador.</span><span class="sxs-lookup"><span data-stu-id="e78f5-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="e78f5-146">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="e78f5-146">Windows authentication</span></span>

<span data-ttu-id="e78f5-147">Se a [autenticação do Windows](xref:security/authentication/windowsauth) estiver configurada em seu aplicativo, SignalR poderá usar essa identidade para proteger os hubs.</span><span class="sxs-lookup"><span data-stu-id="e78f5-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="e78f5-148">No entanto, para enviar mensagens a usuários individuais, você precisa adicionar um provedor de ID de usuário personalizado.</span><span class="sxs-lookup"><span data-stu-id="e78f5-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="e78f5-149">O sistema de autenticação do Windows não fornece a declaração de "identificador de nome".</span><span class="sxs-lookup"><span data-stu-id="e78f5-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="e78f5-150"> usa a declaração para determinar o nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="e78f5-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="e78f5-151">Adicione uma nova classe que implementa `IUserIdProvider` e recupere uma das declarações do usuário para usar como o identificador.</span><span class="sxs-lookup"><span data-stu-id="e78f5-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="e78f5-152">Por exemplo, para usar a declaração "Name" (que é o nome de usuário do Windows no formulário `[Domain]\[Username]`), crie a seguinte classe:</span><span class="sxs-lookup"><span data-stu-id="e78f5-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="e78f5-153">Em vez de `ClaimTypes.Name`, você pode usar qualquer valor da `User` (como o identificador de SID do Windows e assim por diante).</span><span class="sxs-lookup"><span data-stu-id="e78f5-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="e78f5-154">O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema.</span><span class="sxs-lookup"><span data-stu-id="e78f5-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="e78f5-155">Caso contrário, uma mensagem destinada a um usuário poderia acabar indo para um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="e78f5-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="e78f5-156">Registre esse componente em seu método de `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e78f5-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="e78f5-157">No cliente .NET, a autenticação do Windows deve ser habilitada definindo a propriedade [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="e78f5-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="e78f5-158">A autenticação do Windows só tem suporte pelo cliente de navegador ao usar o Microsoft Internet Explorer ou o Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="e78f5-158">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="e78f5-159">Usar declarações para personalizar o tratamento de identidades</span><span class="sxs-lookup"><span data-stu-id="e78f5-159">Use claims to customize identity handling</span></span>

<span data-ttu-id="e78f5-160">Um aplicativo que autentica os usuários pode derivar SignalR IDs de usuário de declarações do usuário.</span><span class="sxs-lookup"><span data-stu-id="e78f5-160">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="e78f5-161">Para especificar como SignalR cria IDs de usuário, implemente `IUserIdProvider` e registre a implementação.</span><span class="sxs-lookup"><span data-stu-id="e78f5-161">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="e78f5-162">O código de exemplo demonstra como você usaria declarações para selecionar o endereço de email do usuário como a propriedade de identificação.</span><span class="sxs-lookup"><span data-stu-id="e78f5-162">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="e78f5-163">O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema.</span><span class="sxs-lookup"><span data-stu-id="e78f5-163">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="e78f5-164">Caso contrário, uma mensagem destinada a um usuário poderia acabar indo para um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="e78f5-164">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="e78f5-165">O registro de conta adiciona uma declaração com o tipo `ClaimsTypes.Email` ao banco de dados de identidade ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e78f5-165">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="e78f5-166">Registre esse componente em seu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e78f5-166">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="e78f5-167">Autorizar usuários a acessar hubs e métodos de Hub</span><span class="sxs-lookup"><span data-stu-id="e78f5-167">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="e78f5-168">Por padrão, todos os métodos em um Hub podem ser chamados por um usuário não autenticado.</span><span class="sxs-lookup"><span data-stu-id="e78f5-168">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="e78f5-169">Para exigir autenticação, aplique o atributo [autorizar](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) ao Hub:</span><span class="sxs-lookup"><span data-stu-id="e78f5-169">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="e78f5-170">Você pode usar os argumentos do construtor e as propriedades do atributo `[Authorize]` para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas.</span><span class="sxs-lookup"><span data-stu-id="e78f5-170">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="e78f5-171">Por exemplo, se você tiver uma política de autorização personalizada chamada `MyAuthorizationPolicy` você pode garantir que somente os usuários que correspondem a essa política possam acessar o Hub usando o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e78f5-171">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="e78f5-172">Os métodos de Hub individuais também podem ter o atributo `[Authorize]` aplicado.</span><span class="sxs-lookup"><span data-stu-id="e78f5-172">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="e78f5-173">Se o usuário atual não corresponder à política aplicada ao método, um erro será retornado ao chamador:</span><span class="sxs-lookup"><span data-stu-id="e78f5-173">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="e78f5-174">Usar manipuladores de autorização para personalizar a autorização do método de Hub</span><span class="sxs-lookup"><span data-stu-id="e78f5-174">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="e78f5-175"> fornece um recurso personalizado para manipuladores de autorização quando um método de Hub requer autorização.</span><span class="sxs-lookup"><span data-stu-id="e78f5-175"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="e78f5-176">O recurso é uma instância do `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="e78f5-176">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="e78f5-177">O `HubInvocationContext` inclui o `HubCallerContext`, o nome do método de Hub que está sendo invocado e os argumentos para o método Hub.</span><span class="sxs-lookup"><span data-stu-id="e78f5-177">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="e78f5-178">Considere o exemplo de uma sala de chat que permite a entrada de várias organizações por meio de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e78f5-178">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="e78f5-179">Qualquer pessoa com um conta Microsoft pode entrar no chat, mas somente os membros da organização proprietária devem ser capazes de proibir os usuários ou exibir os históricos de chat dos usuários.</span><span class="sxs-lookup"><span data-stu-id="e78f5-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="e78f5-180">Além disso, talvez queiramos restringir determinadas funcionalidades de determinados usuários.</span><span class="sxs-lookup"><span data-stu-id="e78f5-180">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="e78f5-181">Usando os recursos atualizados do ASP.NET Core 3,0, isso é totalmente possível.</span><span class="sxs-lookup"><span data-stu-id="e78f5-181">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="e78f5-182">Observe como o `DomainRestrictedRequirement` serve como um `IAuthorizationRequirement`personalizado.</span><span class="sxs-lookup"><span data-stu-id="e78f5-182">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="e78f5-183">Agora que o parâmetro de recurso `HubInvocationContext` está sendo passado, a lógica interna pode inspecionar o contexto no qual o Hub está sendo chamado e tomar decisões sobre como permitir que o usuário execute métodos de Hub individuais.</span><span class="sxs-lookup"><span data-stu-id="e78f5-183">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="e78f5-184">No `Startup.ConfigureServices`, adicione a nova política, fornecendo o requisito de `DomainRestrictedRequirement` personalizado como um parâmetro para criar a política de `DomainRestricted`.</span><span class="sxs-lookup"><span data-stu-id="e78f5-184">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="e78f5-185">No exemplo anterior, a classe `DomainRestrictedRequirement` é uma `IAuthorizationRequirement` e sua própria `AuthorizationHandler` para esse requisito.</span><span class="sxs-lookup"><span data-stu-id="e78f5-185">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="e78f5-186">É aceitável dividir esses dois componentes em classes separadas para separar as preocupações.</span><span class="sxs-lookup"><span data-stu-id="e78f5-186">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="e78f5-187">Um benefício da abordagem do exemplo é que não há necessidade de injetar o `AuthorizationHandler` durante a inicialização, pois o requisito e o manipulador são os mesmos.</span><span class="sxs-lookup"><span data-stu-id="e78f5-187">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e78f5-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e78f5-188">Additional resources</span></span>

* [<span data-ttu-id="e78f5-189">Autenticação de token de portador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e78f5-189">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="e78f5-190">Autorização baseada em recursos</span><span class="sxs-lookup"><span data-stu-id="e78f5-190">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
