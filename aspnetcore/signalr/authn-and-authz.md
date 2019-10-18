---
title: Autenticação e autorização no Signalr ASP.NET Core
author: bradygaster
description: Saiba como usar a autenticação e a autorização no Signalr ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/17/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: 258b6d92896d38b79116278abb7c70b6063e8131
ms.sourcegitcommit: ce2bfb01f2cc7dd83f8a97da0689d232c71bcdc4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531175"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Autenticação e autorização no Signalr ASP.NET Core

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse)

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Autenticar usuários que se conectam a um Hub do Signalr

O signalr pode ser usado com [ASP.NET Core autenticação](xref:security/authentication/identity) para associar um usuário a cada conexão. Em um Hub, os dados de autenticação podem ser acessados por meio da propriedade [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . A autenticação permite que o Hub Chame métodos em todas as conexões associadas a um usuário. Para obter mais informações, consulte [gerenciar usuários e grupos no signalr](xref:signalr/groups). Várias conexões podem ser associadas a um único usuário.

Veja a seguir um exemplo de `Startup.Configure` que usa a autenticação de sinalização e de ASP.NET Core:

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
> A ordem na qual você registra o Signalr e o middleware de autenticação ASP.NET Core é importante. Sempre chame `UseAuthentication` antes `UseSignalR` para que o Signalr tenha um usuário no `HttpContext`.

::: moniker-end

### <a name="cookie-authentication"></a>Autenticação de cookie

Em um aplicativo baseado em navegador, a autenticação de cookie permite que suas credenciais de usuário existentes fluam automaticamente para conexões de Signalr. Ao usar o cliente de navegador, nenhuma configuração adicional é necessária. Se o usuário estiver conectado ao seu aplicativo, a conexão do Signalr herdará automaticamente essa autenticação.

Cookies são uma maneira específica do navegador de enviar tokens de acesso, mas os clientes sem navegador podem enviá-los. Ao usar o [cliente .net](xref:signalr/dotnet-client), a propriedade `Cookies` pode ser configurada na chamada `.WithUrl` para fornecer um cookie. No entanto, usar a autenticação de cookie do cliente .NET requer que o aplicativo forneça uma API para trocar dados de autenticação para um cookie.

### <a name="bearer-token-authentication"></a>Autenticação de token de portador

O cliente pode fornecer um token de acesso em vez de usar um cookie. O servidor valida o token e o usa para identificar o usuário. Essa validação é feita somente quando a conexão é estabelecida. Durante a vida útil da conexão, o servidor não é revalidado automaticamente para verificar a revogação de tokens.

No servidor, a autenticação de token de portador é configurada usando o [middleware portador JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

No cliente JavaScript, o token pode ser fornecido usando a opção [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

No cliente .NET, há uma propriedade [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) semelhante que pode ser usada para configurar o token:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> A função de token de acesso que você fornece é chamada antes de **cada** solicitação HTTP feita pelo signalr. Se você precisar renovar o token para manter a conexão ativa (porque ela pode expirar durante a conexão), faça isso de dentro dessa função e retorne o token atualizado.

Nas APIs Web padrão, os tokens de portador são enviados em um cabeçalho HTTP. No entanto, o Signalr não pode definir esses cabeçalhos em navegadores ao usar alguns transportes. Ao usar Websockets e eventos enviados pelo servidor, o token é transmitido como um parâmetro de cadeia de caracteres de consulta. Para dar suporte a isso no servidor, é necessária uma configuração adicional:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> A cadeia de caracteres de consulta é usada em navegadores ao se conectar com WebSockets e eventos enviados pelo servidor devido a limitações da API do navegador. Ao usar HTTPS, os valores de cadeia de caracteres de consulta são protegidos pela conexão TLS. No entanto, muitos servidores registram valores de cadeia de caracteres de consulta. Para obter mais informações, consulte [considerações de segurança no signalr ASP.NET Core](xref:signalr/security). O signalr usa cabeçalhos para transmitir tokens em ambientes que dão suporte a eles (como os clientes .NET e Java).

### <a name="cookies-vs-bearer-tokens"></a>Cookies versus tokens de portador 

Os cookies são específicos para os navegadores. Enviá-los de outros tipos de clientes adiciona complexidade comparada ao envio de tokens de portador. Consequentemente, a autenticação de cookie não é recomendada, a menos que o aplicativo precise apenas autenticar usuários do cliente de navegador. A autenticação de token de portador é a abordagem recomendada ao usar clientes diferentes do cliente de navegador.

### <a name="windows-authentication"></a>Autenticação do Windows

Se a [autenticação do Windows](xref:security/authentication/windowsauth) estiver configurada em seu aplicativo, o signalr poderá usar essa identidade para proteger os hubs. No entanto, para enviar mensagens a usuários individuais, você precisa adicionar um provedor de ID de usuário personalizado. O sistema de autenticação do Windows não fornece a declaração de "identificador de nome". O signalr usa a declaração para determinar o nome de usuário.

Adicione uma nova classe que implementa `IUserIdProvider` e recupere uma das declarações do usuário para usar como o identificador. Por exemplo, para usar a declaração "Name" (que é o nome de usuário do Windows no formulário `[Domain]\[Username]`), crie a seguinte classe:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Em vez de `ClaimTypes.Name`, você pode usar qualquer valor da `User` (como o identificador de SID do Windows e assim por diante).

> [!NOTE]
> O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema. Caso contrário, uma mensagem destinada a um usuário poderia acabar indo para um usuário diferente.

Registre esse componente em seu método de `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

No cliente .NET, a autenticação do Windows deve ser habilitada definindo a propriedade [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

A autenticação do Windows só tem suporte pelo cliente de navegador ao usar o Microsoft Internet Explorer ou o Microsoft Edge.

### <a name="use-claims-to-customize-identity-handling"></a>Usar declarações para personalizar o tratamento de identidades

Um aplicativo que autentica os usuários pode derivar IDs de usuário do Signalr de declarações do usuário. Para especificar como o Signalr cria IDs de usuário, implemente `IUserIdProvider` e registre a implementação.

O código de exemplo demonstra como você usaria declarações para selecionar o endereço de email do usuário como a propriedade de identificação. 

> [!NOTE]
> O valor escolhido deve ser exclusivo entre todos os usuários em seu sistema. Caso contrário, uma mensagem destinada a um usuário poderia acabar indo para um usuário diferente.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

O registro de conta adiciona uma declaração com o tipo `ClaimsTypes.Email` ao banco de dados de identidade ASP.NET.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registre esse componente em seu `Startup.ConfigureServices`.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorizar usuários a acessar hubs e métodos de Hub

Por padrão, todos os métodos em um Hub podem ser chamados por um usuário não autenticado. Para exigir autenticação, aplique o atributo [autorizar](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) ao Hub:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Você pode usar os argumentos do construtor e as propriedades do atributo `[Authorize]` para restringir o acesso a apenas os usuários que correspondem a [políticas de autorização](xref:security/authorization/policies)específicas. Por exemplo, se você tiver uma política de autorização personalizada chamada `MyAuthorizationPolicy` você pode garantir que somente os usuários que correspondem a essa política possam acessar o Hub usando o seguinte código:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Os métodos de Hub individuais também podem ter o atributo `[Authorize]` aplicado. Se o usuário atual não corresponder à política aplicada ao método, um erro será retornado ao chamador:

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Usar manipuladores de autorização para personalizar a autorização do método de Hub

O signalr fornece um recurso personalizado para manipuladores de autorização quando um método de Hub requer autorização. O recurso é uma instância do `HubInvocationContext`. O `HubInvocationContext` inclui o `HubCallerContext`, o nome do método de Hub que está sendo invocado e os argumentos para o método Hub.

Considere o exemplo de uma sala de chat que permite a entrada de várias organizações por meio de Azure Active Directory. Qualquer pessoa com um conta Microsoft pode entrar no chat, mas somente os membros da organização proprietária devem ser capazes de proibir os usuários ou exibir os históricos de chat dos usuários. Além disso, talvez queiramos restringir determinadas funcionalidades de determinados usuários. Usando os recursos atualizados do ASP.NET Core 3,0, isso é totalmente possível. Observe como o `DomainRestrictedRequirement` serve como um `IAuthorizationRequirement` personalizado. Agora que o parâmetro de recurso `HubInvocationContext` está sendo passado, a lógica interna pode inspecionar o contexto no qual o Hub está sendo chamado e tomar decisões sobre como permitir que o usuário execute métodos de Hub individuais.

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

No `Startup.ConfigureServices`, adicione a nova política, fornecendo o requisito de `DomainRestrictedRequirement` personalizado como um parâmetro para criar a política de `DomainRestricted`.

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

No exemplo anterior, a classe `DomainRestrictedRequirement` é uma `IAuthorizationRequirement` e sua própria `AuthorizationHandler` para esse requisito. É aceitável dividir esses dois componentes em classes separadas para separar as preocupações. Um benefício da abordagem do exemplo é que não há necessidade de injetar o `AuthorizationHandler` durante a inicialização, pois o requisito e o manipulador são os mesmos.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Autenticação de token de portador no ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Autorização baseada em recursos](xref:security/authorization/resourcebased)
