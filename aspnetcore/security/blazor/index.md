---
title: Autenticação e autorização do ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre os cenários de autenticação e autorização do Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/21/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: f7ffb4c3d5a05cb916b4f00cdfaf5898634a1a6d
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219019"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>Autorização e autenticação no Blazor em ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em aplicativos Blazor.

Os cenários de segurança diferem entre o servidor mais incrivelmente e os aplicativos Webassembly de mais incrivelmente. Como os aplicativos de servidor mais amseriais executados no servidor, as verificações de autorização são capazes de determinar:

* As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).
* As regras de acesso para áreas do aplicativo e componentes.

Aplicativos Webassembly de mais de-vela são executados no cliente. A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas. Como as verificações do lado do cliente podem ser modificadas ou ignoradas por um usuário, um aplicativo Webassembly de mais alto que não pode impor regras de acesso de autorização.

[Razor Pages as convenções de autorização](xref:security/authorization/razor-pages-authorization) não se aplicam a componentes do Razor roteáveis. Se um componente Razor não roteável for [inserido em uma página](xref:blazor/integrate-components#render-components-from-a-page-or-view), as convenções de autorização da página afetarão indiretamente o componente Razor junto com o restante do conteúdo da página.

## <a name="authentication"></a>Autenticação

O Blazor usa os mecanismos de autenticação do ASP.NET Core existentes para estabelecer a identidade do usuário. O mecanismo exato depende de como o aplicativo mais incrivelmente é hospedado, um servidor mais incrivelmente ou um Webassembly de mais incrivelmente.

### <a name="blazor-server-authentication"></a>Autenticação de servidor mais incrivelmente

Os aplicativos de servidor mais incrivelmente operam em uma conexão em tempo real que é criada usando o Signalr. A [autenticação em aplicativos baseados em SignalR](xref:signalr/authn-and-authz) é realizada quando a conexão é estabelecida. A autenticação pode ser baseada em um cookie ou um algum outro token de portador.

O modelo de projeto de servidor mais incrivelmente pode configurar a autenticação para você quando o projeto é criado.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga as diretrizes do Visual Studio no artigo de <xref:blazor/get-started> para criar um novo projeto de servidor com um mecanismo de autenticação.

Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.

Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:

* **Sem Autenticação**
* **Contas de usuário individuais** &ndash; contas de usuário podem ser armazenadas:
  * Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.
  * Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Contas corporativas ou de estudante**
* **Autenticação do Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga as diretrizes de Visual Studio Code no artigo de <xref:blazor/get-started> para criar um novo projeto de servidor com um mecanismo de autenticação:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação                                                                 | `{AUTHENTICATION}` valor |
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

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
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

### <a name="opno-locblazor-webassembly-authentication"></a>autenticação de Webassembly Blazor

Em Blazor aplicativos Webassembly, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

Adicione uma referência de pacote para [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) ao arquivo de projeto do aplicativo.

A implementação de um serviço de `AuthenticationStateProvider` personalizado para aplicativos Webassembly Blazor é abordada nas seções a seguir.

## <a name="authenticationstateprovider-service"></a>Serviço AuthenticationStateProvider

os aplicativos do Blazor Server incluem um serviço de `AuthenticationStateProvider` interno que obtém dados de estado de autenticação do `HttpContext.User`de ASP.NET Core. Essa é a maneira que o estado de autenticação se integra a mecanismos de autenticação existentes no lado do servidor do ASP.NET Core.

O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.

Normalmente, você não usa o `AuthenticationStateProvider` diretamente. Use as abordagens do [componente AuthorizeView](#authorizeview-component) ou [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) descritas mais adiante neste artigo. A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.

O serviço `AuthenticationStateProvider` pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="LogUsername">Write user info to console</button>

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

Se você estiver criando um aplicativo Webassembly Blazor ou se a especificação do seu aplicativo for absolutamente necessária um provedor personalizado, implemente um provedor e substitua `GetAuthenticationStateAsync`:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
    public class CustomAuthStateProvider : AuthenticationStateProvider
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
}
```

Em um aplicativo Webassembly Blazor, o serviço `CustomAuthStateProvider` é registrado em `Main` do *Program.cs*:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.Extensions.DependencyInjection;
using BlazorSample.Services;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddScoped<AuthenticationStateProvider, 
            CustomAuthStateProvider>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

Ao usarem o `CustomAuthStateProvider`, todos os usuários são autenticados com o nome de usuário `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Expor o estado de autenticação como um parâmetro em cascata

Se os dados do estado de autenticação forem necessários para a lógica do procedimento, como ao realizar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<AuthenticationState>`:

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

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

> [!NOTE]
> Em um componente de aplicativo Webassembly Blazor, adicione o namespace `Microsoft.AspNetCore.Components.Authorization` (`@using Microsoft.AspNetCore.Components.Authorization`).

Se `user.Identity.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.

Configure o `Task<AuthenticationState>` parâmetro em cascata usando os componentes `AuthorizeRouteView` e `CascadingAuthenticationState` no arquivo *app. Razor* :

```razor
@using Microsoft.AspNetCore.Components.Authorization

<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Adicionar serviços para opções e autorização para `Program.Main`:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
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

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Também é possível fornecer um conteúdo diferente para ser exibido caso o usuário não esteja autenticado:

```razor
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

O conteúdo das marcas de `<Authorized>` e `<NotAuthorized>` pode incluir itens arbitrários, como outros componentes interativos.

As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).

Se as condições de autorização não forem especificadas, o `AuthorizeView` usará uma política padrão e tratará:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

### <a name="role-based-and-policy-based-authorization"></a>Autorização baseada em funções e em políticas

O componente `AuthorizeView` dá suporte à autorização *baseada em funções* ou *baseada em políticas*.

Para a autorização baseada em funções, use o parâmetro `Roles`:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Para obter mais informações, consulte <xref:security/authorization/roles>.

Para a autorização baseada em políticas, use o parâmetro `Policy`:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

A autorização baseada em declarações é um caso especial de autorização baseada em políticas. Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração. Para obter mais informações, consulte <xref:security/authorization/policies>.

Essas APIs podem ser usadas em um servidor Blazor ou Blazor aplicativos Webassembly.

Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.

### <a name="content-displayed-during-asynchronous-authentication"></a>Conteúdo exibido durante a autenticação assíncrona

Blazor permite que o estado de autenticação seja determinado de *forma assíncrona*. O cenário principal para essa abordagem é em Blazor aplicativos Webassembly que fazem uma solicitação para um ponto de extremidade externo para autenticação.

Enquanto a autenticação estiver em andamento, `AuthorizeView` não exibirá nenhum conteúdo por padrão. Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:

```razor
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

Essa abordagem normalmente não é aplicável a aplicativos Blazor Server. os aplicativos do Blazor Server conhecem o estado de autenticação assim que o estado é estabelecido. `Authorizing` conteúdo pode ser fornecido em um componente de `AuthorizeView` do aplicativo Blazor Server, mas o conteúdo nunca é exibido.

## <a name="authorize-attribute"></a>Atributo [Authorize]

O atributo `[Authorize]` pode ser usado em componentes do Razor:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> Em um componente de aplicativo Webassembly Blazor, adicione o namespace `Microsoft.AspNetCore.Authorization` (`@using Microsoft.AspNetCore.Authorization`) aos exemplos nesta seção.

> [!IMPORTANT]
> Use apenas `[Authorize]` em componentes `@page` atingidos por meio do roteador Blazor. A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página. Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.

O atributo `[Authorize]` também dá suporte à autorização baseada em funções ou em políticas. Para a autorização baseada em funções, use o parâmetro `Roles`:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Para a autorização baseada em políticas, use o parâmetro `Policy`:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Se `Roles` e `Policy` não forem especificados, `[Authorize]` usará a política padrão, que tratará:

* Usuários autenticados (conectados) como autorizados.
* Usuários não autenticados (não conectados) como não autorizados.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personalizar conteúdo não autorizado com o componente Router

O componente `Router`, em conjunto com o componente `AuthorizeRouteView`, permite que o aplicativo especifique conteúdo personalizado se:

* O conteúdo não for encontrado.
* O usuário não atender à condição `[Authorize]` aplicada ao componente. O atributo `[Authorize]` é abordado na seção [atributo de`[Authorize]`](#authorize-attribute) .
* A autenticação assíncrona estiver em andamento.

No modelo de projeto padrão do Blazor Server, o arquivo *app. Razor* demonstra como definir o conteúdo personalizado:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

O conteúdo das marcas `<NotFound>`, `<NotAuthorized>`e `<Authorizing>` pode incluir itens arbitrários, como outros componentes interativos.

Se o elemento `<NotAuthorized>` não for especificado, o `AuthorizeRouteView` usará a seguinte mensagem de fallback:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notificação sobre mudanças no estado de autenticação

Se o aplicativo determinar que os dados sobre o estado de autenticação subjacente foram alterados (por exemplo, porque o usuário se desconectou ou porque outro usuário alterou suas funções), um `AuthenticationStateProvider` personalizado poderá invocar opcionalmente o método `NotifyAuthenticationStateChanged` na classe base `AuthenticationStateProvider`. Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.

## <a name="procedural-logic"></a>Lógica de procedimento

Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário. O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.

```razor
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

> [!NOTE]
> Em um componente de aplicativo Webassembly Blazor, adicione os namespaces de `Microsoft.AspNetCore.Authorization` e `Microsoft.AspNetCore.Components.Authorization`:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autorização em aplicativos Webassembly Blazor

Em Blazor aplicativos Webassembly, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**

Para obter mais informações, consulte os artigos em <xref:security/blazor/webassembly/index>.

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Erros comuns:

* **A autorização requer um parâmetro em cascata do tipo Task\<Authenticationstate >. Considere o uso de CascadingAuthenticationState para fornecer isso.**

* **O valor `null` é recebido para `authenticationStateTask`**

É provável que o projeto não tenha sido criado usando um modelo de servidor Blazor com autenticação habilitada. Encapsule um `<CascadingAuthenticationState>` em torno de alguma parte da árvore da interface do usuário, por exemplo, em *App.razor*, da seguinte maneira:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

O `CascadingAuthenticationState` fornece o parâmetro em cascata `Task<AuthenticationState>` que, por sua vez, ele recebe do serviço DI subjacente `AuthenticationStateProvider`.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Awesome Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) links de exemplo da comunidade de autenticação
