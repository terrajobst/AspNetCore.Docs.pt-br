---
title: Cenários de segurança adicionais do Webassembly ASP.NET Core Blazor
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: ccb512392341e3eea33f4ab45740b7900f7b63f9
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989472"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Cenários de segurança adicionais do Webassembly ASP.NET Core mais incrivelmente

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a>Manipular erros de solicitação de token

Quando um aplicativo de página única (SPA) autentica um usuário usando o OIDC (Open ID Connect), o estado de autenticação é mantido localmente no SPA e no provedor de identidade (IP) na forma de um cookie de sessão que é definido como resultado do usuário que fornece seu fornecidas.

Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens. Caso contrário, o usuário será desconectado após os tokens concedidos expirarem. Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.

Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP. Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido. Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.

Esses cenários não são específicos para a autenticação baseada em token. Eles fazem parte da natureza do SPAs. Um SPA que usa cookies também falha ao chamar uma API de servidor se o cookie de autenticação for removido.

Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:

* Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.
* Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.

Quando o aplicativo solicita um token, há dois resultados possíveis:

* A solicitação é realizada com sucesso e o aplicativo tem um token válido.
* A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.

Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento. Existem várias abordagens com níveis crescentes de complexidade:

* Armazene o estado da página atual no armazenamento de sessão. Durante a `OnInitializeAsync`, verifique se o estado pode ser restaurado antes de continuar.
* Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.
* Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.

O exemplo a seguir mostra como:

* Preserve o estado antes de redirecionar para a página de logon.
* Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>Salvar o estado do aplicativo antes de uma operação de autenticação

Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP. Esse pode ser o caso quando você estiver usando algo como um contêiner de estado e desejar restaurar o estado depois que a autenticação for realizada com sucesso. Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.

componente `Authentication` (*páginas/autenticação. Razor*):

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="request-additional-access-tokens"></a>Solicitar tokens de acesso adicionais

A maioria dos aplicativos requer apenas um token de acesso para interagir com os recursos protegidos que eles usam. Em alguns cenários, um aplicativo pode exigir mais de um token para interagir com dois ou mais recursos. O método `IAccessTokenProvider.RequestToken` fornece uma sobrecarga que permite que um aplicativo provisione um token com um determinado conjunto de escopos, como mostrado no exemplo a seguir:

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a>Personalizar rotas de aplicativo

Por padrão, a biblioteca de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.

| Rota                            | Finalidade |
| -------------------------------- | ------- |
| `authentication/login`           | Dispara uma operação de entrada. |
| `authentication/login-callback`  | Manipula o resultado de qualquer operação de entrada. |
| `authentication/login-failed`    | Exibe mensagens de erro quando a operação de entrada falha por algum motivo. |
| `authentication/logout`          | Dispara uma operação de saída. |
| `authentication/logout-callback` | Lida com o resultado de uma operação de saída. |
| `authentication/logout-failed`   | Exibe mensagens de erro quando a operação de saída falha por algum motivo. |
| `authentication/logged-out`      | Indica que o usuário fez logoff com êxito. |
| `authentication/profile`         | Dispara uma operação para editar o perfil do usuário. |
| `authentication/register`        | Dispara uma operação para registrar um novo usuário. |

As rotas mostradas na tabela anterior são configuráveis por meio de `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`. Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.

No exemplo a seguir, todos os caminhos são prefixados com `/security`.

componente `Authentication` (*páginas/autenticação. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (*Program.cs*):

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o `RemoteAuthenticatorView` com um parâmetro de ação explícita:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.

## <a name="customize-the-authentication-user-interface"></a>Personalizar a interface do usuário de autenticação

`RemoteAuthenticatorView` inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação. Cada Estado pode ser personalizado passando um `RenderFragment`personalizado. Para personalizar o texto exibido durante o processo de logon inicial, o pode alterar o `RemoteAuthenticatorView` da seguinte maneira.

componente `Authentication` (*páginas/autenticação. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

O `RemoteAuthenticatorView` tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.

| Rota                            | Fragmento                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
