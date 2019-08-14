---
title: Autorização e autenticação no Blazor em ASP.NET Core
author: guardrex
description: Saiba mais sobre os cenários de autenticação e autorização no Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/26/2019
uid: security/blazor/index
ms.openlocfilehash: 87d61a7ccda209243a62bc54467b8f02dad92c24
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994195"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>Autorização e autenticação no Blazor em ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em aplicativos Blazor.

Os cenários de segurança diferem entre aplicativos Blazor do lado do servidor e do lado do cliente. Como os aplicativos Blazor do lado do servidor são executados no servidor, as verificações de autorização podem determinar:

* As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).
* As regras de acesso para áreas do aplicativo e componentes.

Os aplicativos Blazor do lado do cliente são executados no cliente. A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas. Uma vez que as verificações do lado do cliente podem ser modificadas ou ignoradas pelo usuário, o aplicativo Blazor do lado do cliente não pode aplicar regras de autorização de acesso.

## <a name="authentication"></a>Autenticação

O Blazor usa os mecanismos de autenticação do ASP.NET Core existentes para estabelecer a identidade do usuário. O mecanismo exato depende de como o aplicativo Blazor está hospedado, no lado do servidor ou do cliente.

### <a name="blazor-server-side-authentication"></a>Autenticação no Blazor do lado do servidor

Os aplicativos Blazor do lado do servidor operam em uma conexão em tempo real criada usando SignalR. A [autenticação em aplicativos baseados em SignalR](xref:signalr/authn-and-authz) é realizada quando a conexão é estabelecida. A autenticação pode ser baseada em um cookie ou um algum outro token de portador.

O modelo de projeto Blazor do lado do servidor pode configurar a autenticação para você quando o projeto é criado.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga as orientações do Visual Studio no artigo <xref:blazor/get-started> para criar um novo projeto Blazor do lado do servidor com um mecanismo de autenticação.

Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.

Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:

* **Sem Autenticação**
* **Contas de usuário individuais** &ndash; as contas de usuário podem ser armazenadas:
  * Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.
  * Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Contas corporativas ou de estudante**
* **Autenticação do Windows**

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga as orientações do Visual Studio no artigo <xref:blazor/get-started> para criar um novo projeto Blazor do lado do servidor com um mecanismo de autenticação:

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação                                                                 | Valor `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Sem Autenticação                                                                        | `None`                   |
| Individual<br>Usuários armazenados no aplicativo com o ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Usuários armazenados no [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Contas corporativas ou de estudante<br>Autenticação organizacional para um único locatário.            | `SingleOrg`              |
| Contas corporativas ou de estudante<br>Autenticação organizacional para vários locatários.           | `MultiOrg`               |
| Autenticação do Windows                                                                   | `Windows`                |

O comando cria uma pasta com nome do valor fornecido para o espaço reservado `{APP NAME}` e usa o nome da pasta como o nome do aplicativo. Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="blazor-client-side-authentication"></a>Autenticação no Blazor do lado do cliente

Em aplicativos Blazor do lado do cliente, as verificações de autenticação podem ser ignoradas porque todos os códigos do lado do cliente podem ser modificados pelos usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

A implementação de um serviço `AuthenticationStateProvider` personalizado para aplicativos Blazor do lado do cliente é abordada nas seções a seguir.

## <a name="authenticationstateprovider-service"></a>Serviço AuthenticationStateProvider

Os aplicativos Blazor do lado do servidor incluem um serviço `AuthenticationStateProvider` interno que obtém dados do estado de autenticação do `HttpContext.User` do ASP.NET Core. Essa é a maneira que o estado de autenticação se integra a mecanismos de autenticação existentes no lado do servidor do ASP.NET Core.

O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.

Normalmente, você não usa o `AuthenticationStateProvider` diretamente. Use as abordagens do [componente AuthorizeView](#authorizeview-component) ou [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) descritas mais adiante neste artigo. A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.

O serviço `AuthenticationStateProvider` pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:

```cshtml
@page "/"
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

Se `user.Identity.IsAuthenticated` for `true` e como o usuário é um <xref:System.Security.Claims.ClaimsPrincipal>, será possível enumerar as declarações e avaliar a associação nas funções.

Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementar um AuthenticationStateProvider personalizado

Se você estiver criando um aplicativo Blazor do lado do cliente ou se a especificação do seu aplicativo exigir completamente um provedor personalizado, implemente um provedor e substitua `GetAuthenticationStateAsync`:

```csharp
class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

O serviço `CustomAuthStateProvider` é registrado em `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

Ao usarem o `CustomAuthStateProvider`, todos os usuários são autenticados com o nome de usuário `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Expor o estado de autenticação como um parâmetro em cascata

Se os dados do estado de autenticação forem necessários para a lógica do procedimento, como ao realizar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<AuthenticationState>`:

```cshtml
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

Se `user.Identity.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.

Configure o parâmetro em cascata `Task<AuthenticationState>` usando o componente `CascadingAuthenticationState`:

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        <NotFoundContent>
            <h1>Sorry</h1>
            <p>Sorry, there's nothing at this address.</p>
        </NotFoundContent>
    </Router>
</CascadingAuthenticationState>
```

## <a name="authorization"></a>Autorização

Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.

O acesso geralmente é concedido ou negado com base nos seguintes casos:

* Se o usuário está autenticado (conectado).
* Se o usuário está em uma *função*.
* Se o usuário tem uma *declaração*.
* Se uma *política* é atendida.

Todos esses conceitos são iguais no MVC do ASP.NET Core ou em aplicativos Razor Pages. Para obter mais informações sobre a segurança do ASP.NET Core, confira os artigos em [Identidade e segurança do ASP.NET Core](xref:security/index).

## <a name="authorizeview-component"></a>Componente AuthorizeView

O componente `AuthorizeView` exibe de forma seletiva a interface do usuário, caso o usuário esteja autorizado a vê-la. Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.

O componente expõe uma variável `context` do tipo `AuthenticationState`, que pode ser usada para acessar informações sobre o usuário conectado:

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Também é possível fornecer um conteúdo diferente para ser exibido caso o usuário não esteja autenticado:

```cshtml
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

O conteúdo de `<Authorized>` e `<NotAuthorized>` pode incluir itens arbitrários, como outros componentes interativos.

As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).

Se as condições de autorização não forem especificadas, o `AuthorizeView` usará uma política padrão e tratará:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

### <a name="role-based-and-policy-based-authorization"></a>Autorização baseada em funções e em políticas

O componente `AuthorizeView` dá suporte à autorização *baseada em funções* ou *baseada em políticas*.

Para a autorização baseada em funções, use o parâmetro `Roles`:

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Para obter mais informações, consulte <xref:security/authorization/roles>.

Para a autorização baseada em políticas, use o parâmetro `Policy`:

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

A autorização baseada em declarações é um caso especial de autorização baseada em políticas. Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração. Para obter mais informações, consulte <xref:security/authorization/policies>.

Essas APIs podem ser usadas em aplicativos Blazor do lado do cliente ou naqueles do lado do servidor.

Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.

### <a name="content-displayed-during-asynchronous-authentication"></a>Conteúdo exibido durante a autenticação assíncrona

O Blazor permite que o estado de autenticação seja determinado *assincronamente*. O cenário principal dessa abordagem ocorre em aplicativos Blazor do lado do cliente que fazem uma solicitação a um ponto de extremidade externo para autenticação.

Enquanto a autenticação estiver em andamento, `AuthorizeView` não exibirá nenhum conteúdo por padrão. Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:

```cshtml
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Normalmente, essa abordagem não é aplicável a aplicativos Blazor do lado do servidor. Os aplicativos Blazor do lado do servidor ficam conhecendo o estado de autenticação assim que ele é estabelecido. O conteúdo `Authorizing` pode ser fornecido em um componente `AuthorizeView` do aplicativo Blazor do lado do servidor, mas o conteúdo nunca é exibido.

## <a name="authorize-attribute"></a>Atributo [Authorize]

Assim como um aplicativo pode usar `[Authorize]` com um controlador MVC ou Razor Page, `[Authorize]` também pode ser usado com componentes Razor:

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Use `[Authorize]` somente em componentes `@page` acessados por meio do roteador Blazor. A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página. Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.

Talvez você precise adicionar `@using Microsoft.AspNetCore.Authorization` ao componente ou ao arquivo *_Imports.razor* para compilar o componente.

O atributo `[Authorize]` também dá suporte à autorização baseada em funções ou em políticas. Para a autorização baseada em funções, use o parâmetro `Roles`:

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Para a autorização baseada em políticas, use o parâmetro `Policy`:

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Se `Roles` e `Policy` não forem especificados, `[Authorize]` usará a política padrão, que tratará:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personalizar conteúdo não autorizado com o componente Router

O componente `Router` permitirá que o aplicativo especifique o conteúdo personalizado se:

* O conteúdo não for encontrado.
* O usuário não atender à condição `[Authorize]` aplicada ao componente. O atributo `[Authorize]` é abordado na seção [Atributo [Authorize]](#authorize-attribute).
* A autenticação assíncrona estiver em andamento.

No modelo de projeto Blazor padrão do lado do servidor, o arquivo *App.razor* demonstra como definir o conteúdo personalizado:

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        <NotFoundContent>
            <h1>Sorry</h1>
            <p>Sorry, there's nothing at this address.</p>
        </NotFoundContent>
        <NotAuthorizedContent>
            <h1>Sorry</h1>
            <p>You're not authorized to reach this page.</p>
            <p>You may need to log in as a different user.</p>
        </NotAuthorizedContent>
        <AuthorizingContent>
            <h1>Authentication in progress</h1>
            <p>Only visible while authentication is in progress.</p>
        </AuthorizingContent>
    </Router>
</CascadingAuthenticationState>
```

O conteúdo de `<NotFoundContent>`, `<NotAuthorizedContent>` e `<AuthorizingContent>` pode incluir itens arbitrários, como outros componentes interativos.

Se `<NotAuthorizedContent>` não for especificado, o roteador usará a seguinte mensagem de fallback:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notificação sobre mudanças no estado de autenticação

Se o aplicativo determinar que os dados sobre o estado de autenticação subjacente foram alterados (por exemplo, porque o usuário se desconectou ou porque outro usuário alterou suas funções), um `AuthenticationStateProvider` personalizado poderá invocar opcionalmente o método `NotifyAuthenticationStateChanged` na classe base `AuthenticationStateProvider`. Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.

## <a name="procedural-logic"></a>Lógica de procedimento

Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário. O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.

```cshtml
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

## <a name="authorization-in-blazor-client-side-apps"></a>Autorização em aplicativos Blazor do lado do cliente

Em aplicativos Blazor do lado do cliente, as verificações de autorização podem ser ignoradas porque todos os códigos do lado do cliente podem ser modificados pelos usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Erros comuns:

* **A autorização requer um parâmetro em cascata do tipo Task\<AuthenticationState>. Considere utilizar CascadingAuthenticationState para fornecê-la.**

* **O valor `null` é recebido para `authenticationStateTask`**

É provável que o projeto não tenha sido criado usando um modelo Blazor do lado do servidor com a autenticação habilitada. Encapsule um `<CascadingAuthenticationState>` em torno de alguma parte da árvore da interface do usuário, por exemplo, em *App.razor*, da seguinte maneira:

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

O `CascadingAuthenticationState` fornece o parâmetro em cascata `Task<AuthenticationState>` que, por sua vez, ele recebe do serviço DI subjacente `AuthenticationStateProvider`.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/index>
* <xref:security/authentication/windowsauth>
