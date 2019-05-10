---
title: Autenticação e autorização no SignalR do ASP.NET Core
author: bradygaster
description: Saiba como usar a autenticação e autorização no SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/09/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e8f9dc48be780fb91bdec6ea4d579f5e4f16197b
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516949"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="bc139-103">Autenticação e autorização no SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc139-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="bc139-104">Por [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="bc139-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="bc139-105">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="bc139-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="bc139-106">Autenticar usuários que se conectam a um hub SignalR</span><span class="sxs-lookup"><span data-stu-id="bc139-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="bc139-107">O SignalR pode ser usado com [autenticação do ASP.NET Core](xref:security/authentication/identity) para associar um usuário a cada conexão.</span><span class="sxs-lookup"><span data-stu-id="bc139-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="bc139-108">Em um hub, os dados de autenticação podem ser acessados do [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) propriedade.</span><span class="sxs-lookup"><span data-stu-id="bc139-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="bc139-109">A autenticação permite que o hub para chamar métodos em todas as conexões associadas a um usuário (consulte [gerenciar usuários e grupos no SignalR](xref:signalr/groups) para obter mais informações).</span><span class="sxs-lookup"><span data-stu-id="bc139-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="bc139-110">Várias conexões podem estar associadas um único usuário.</span><span class="sxs-lookup"><span data-stu-id="bc139-110">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="bc139-111">A seguir está um exemplo de `Startup.Configure` que usa a autenticação do SignalR e o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bc139-111">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="bc139-112">A ordem em que você registre o middleware de autenticação SignalR e o ASP.NET Core é importante.</span><span class="sxs-lookup"><span data-stu-id="bc139-112">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="bc139-113">Sempre chamar `UseAuthentication` antes de `UseSignalR` para que um usuário tenha o SignalR `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="bc139-113">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="bc139-114">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="bc139-114">Cookie authentication</span></span>

<span data-ttu-id="bc139-115">Em um aplicativo baseado em navegador, a autenticação de cookie permite que suas credenciais de usuário existentes fluir automaticamente para conexões do SignalR.</span><span class="sxs-lookup"><span data-stu-id="bc139-115">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="bc139-116">Ao usar o cliente de navegador, nenhuma configuração adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="bc139-116">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="bc139-117">Se o usuário está conectado ao seu aplicativo, a conexão do SignalR herda automaticamente essa autenticação.</span><span class="sxs-lookup"><span data-stu-id="bc139-117">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="bc139-118">Os cookies são uma maneira de específicas do navegador para enviar os tokens de acesso, mas os clientes sem navegador podem enviá-los.</span><span class="sxs-lookup"><span data-stu-id="bc139-118">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="bc139-119">Ao usar o [cliente .NET](xref:signalr/dotnet-client), o `Cookies` propriedade pode ser configurada no `.WithUrl` chamada para fornecer um cookie.</span><span class="sxs-lookup"><span data-stu-id="bc139-119">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="bc139-120">No entanto, usando a autenticação de cookie do cliente .NET requer que o aplicativo para fornecer uma API para trocar dados de autenticação para um cookie.</span><span class="sxs-lookup"><span data-stu-id="bc139-120">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="bc139-121">Autenticação de token de portador</span><span class="sxs-lookup"><span data-stu-id="bc139-121">Bearer token authentication</span></span>

<span data-ttu-id="bc139-122">O cliente pode fornecer um token de acesso em vez de usar um cookie.</span><span class="sxs-lookup"><span data-stu-id="bc139-122">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="bc139-123">O servidor valida o token e usa-o para identificar o usuário.</span><span class="sxs-lookup"><span data-stu-id="bc139-123">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="bc139-124">Essa validação é feita somente quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="bc139-124">This validation is done only when the connection is established.</span></span> <span data-ttu-id="bc139-125">Durante a vida da conexão, o servidor não automaticamente revalidar para verificar a revogação do token.</span><span class="sxs-lookup"><span data-stu-id="bc139-125">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="bc139-126">No servidor de autenticação de token de portador é configurada usando o [middleware de portador de JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="bc139-126">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="bc139-127">No cliente JavaScript, o token pode ser fornecido usando o [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) opção.</span><span class="sxs-lookup"><span data-stu-id="bc139-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="bc139-128">No cliente do .NET, há um semelhante [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) propriedade que pode ser usada para configurar o token:</span><span class="sxs-lookup"><span data-stu-id="bc139-128">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="bc139-129">A função de token de acesso que você fornecer é chamada antes de **cada** solicitação HTTP feita pelo SignalR.</span><span class="sxs-lookup"><span data-stu-id="bc139-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="bc139-130">Se você precisa renovar o token para manter a conexão ativa (porque ele pode expirar durante a conexão), faça isso dentro dessa função e retornar o token atualizado.</span><span class="sxs-lookup"><span data-stu-id="bc139-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="bc139-131">APIs da web padrão, os tokens de portador são enviados em um cabeçalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="bc139-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="bc139-132">No entanto, o SignalR é não é possível definir esses cabeçalhos em navegadores quando usando alguns transportes.</span><span class="sxs-lookup"><span data-stu-id="bc139-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="bc139-133">Ao usar WebSockets e eventos do Server-Sent, o token é transmitido como um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="bc139-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="bc139-134">Para suportar isso no servidor, a configuração adicional é necessária:</span><span class="sxs-lookup"><span data-stu-id="bc139-134">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="bc139-135">Cookies versus os tokens de portador</span><span class="sxs-lookup"><span data-stu-id="bc139-135">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="bc139-136">Como os cookies são específicos para navegadores, enviá-las de outros tipos de clientes adiciona complexidade em comparação comparada enviar tokens de portador.</span><span class="sxs-lookup"><span data-stu-id="bc139-136">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="bc139-137">Por esse motivo, a autenticação de cookie não é recomendada, a menos que o aplicativo precisa apenas para autenticar usuários de cliente de navegador.</span><span class="sxs-lookup"><span data-stu-id="bc139-137">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="bc139-138">Autenticação de token de portador é a abordagem recomendada ao usar os clientes que não seja o cliente do navegador.</span><span class="sxs-lookup"><span data-stu-id="bc139-138">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="bc139-139">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="bc139-139">Windows authentication</span></span>

<span data-ttu-id="bc139-140">Se [autenticação do Windows](xref:security/authentication/windowsauth) é configurado em seu aplicativo, o SignalR pode usar essa identidade para proteger hubs.</span><span class="sxs-lookup"><span data-stu-id="bc139-140">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="bc139-141">No entanto, para enviar mensagens para usuários individuais, você precisará adicionar um provedor de ID de usuário personalizado.</span><span class="sxs-lookup"><span data-stu-id="bc139-141">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="bc139-142">Isso ocorre porque o sistema de autenticação do Windows não fornece a declaração "Identificador de nome" que usa o SignalR para determinar o nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="bc139-142">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="bc139-143">Adicione uma nova classe que implementa `IUserIdProvider` e recuperar uma das declarações de usuário a ser usado como o identificador.</span><span class="sxs-lookup"><span data-stu-id="bc139-143">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="bc139-144">Por exemplo, para usar a declaração de "Nome" (que é o nome de usuário do Windows na forma `[Domain]\[Username]`), crie a seguinte classe:</span><span class="sxs-lookup"><span data-stu-id="bc139-144">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="bc139-145">Em vez de `ClaimTypes.Name`, você pode usar qualquer valor entre o `User` (como o identificador do SID do Windows, etc.).</span><span class="sxs-lookup"><span data-stu-id="bc139-145">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="bc139-146">O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema.</span><span class="sxs-lookup"><span data-stu-id="bc139-146">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="bc139-147">Caso contrário, uma mensagem para um usuário poderia acabar indo para um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="bc139-147">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="bc139-148">Registrar esse componente em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="bc139-148">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="bc139-149">No cliente do .NET, a autenticação do Windows deve ser habilitada definindo o [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) propriedade:</span><span class="sxs-lookup"><span data-stu-id="bc139-149">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="bc139-150">Autenticação do Windows só tem suporte pelo cliente do navegador ao usar o Microsoft Internet Explorer ou Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="bc139-150">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="bc139-151">Uso de declarações para personalizar o tratamento de identidade</span><span class="sxs-lookup"><span data-stu-id="bc139-151">Use claims to customize identity handling</span></span>

<span data-ttu-id="bc139-152">Um aplicativo que autentica usuários pode derivar as IDs de usuário do SignalR de declarações de usuário.</span><span class="sxs-lookup"><span data-stu-id="bc139-152">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="bc139-153">Para especificar como o SignalR cria IDs de usuário, implementar `IUserIdProvider` e registrar a implementação.</span><span class="sxs-lookup"><span data-stu-id="bc139-153">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="bc139-154">O código de exemplo demonstra como você usaria declarações para selecionar o endereço de email do usuário como a propriedade de identificação.</span><span class="sxs-lookup"><span data-stu-id="bc139-154">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="bc139-155">O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema.</span><span class="sxs-lookup"><span data-stu-id="bc139-155">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="bc139-156">Caso contrário, uma mensagem para um usuário poderia acabar indo para um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="bc139-156">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="bc139-157">O registro de conta adiciona uma declaração com o tipo `ClaimsTypes.Email` no banco de dados de identidade do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="bc139-157">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="bc139-158">Registrar esse componente em seu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bc139-158">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="bc139-159">Autorizar usuários para acesso hubs e métodos de hub</span><span class="sxs-lookup"><span data-stu-id="bc139-159">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="bc139-160">Por padrão, todos os métodos em um hub podem ser chamados por um usuário não autenticado.</span><span class="sxs-lookup"><span data-stu-id="bc139-160">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="bc139-161">Para exigir autenticação, se aplicam a [autorizar](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) de atributo para o hub:</span><span class="sxs-lookup"><span data-stu-id="bc139-161">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="bc139-162">Você pode usar os argumentos de construtor e propriedades do `[Authorize]` atributo para restringir o acesso somente para usuários específicos de correspondência [políticas de autorização](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="bc139-162">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="bc139-163">Por exemplo, se você tiver uma política de autorização personalizada chamada `MyAuthorizationPolicy` pode garantir que somente os usuários dessa política de correspondência podem acessar o hub usando o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bc139-163">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

<span data-ttu-id="bc139-164">Métodos de hub individuais podem ter o `[Authorize]` atributo aplicado também.</span><span class="sxs-lookup"><span data-stu-id="bc139-164">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="bc139-165">Se o usuário atual não corresponder a política aplicada ao método, um erro será retornado ao chamador:</span><span class="sxs-lookup"><span data-stu-id="bc139-165">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub: Hub
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

## <a name="additional-resources"></a><span data-ttu-id="bc139-166">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bc139-166">Additional resources</span></span>

* [<span data-ttu-id="bc139-167">Autenticação de Token de portador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc139-167">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
