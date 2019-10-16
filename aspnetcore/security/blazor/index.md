---
title: Autorização e autenticação no Blazor em ASP.NET Core
author: guardrex
description: Saiba mais sobre os cenários de autenticação e autorização no Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/blazor/index
ms.openlocfilehash: 85a6a32ea068e6cd00ebb71bdf7fe0bd06b77618
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391307"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="79de8-103">Autorização e autenticação no Blazor em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79de8-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="79de8-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="79de8-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="79de8-105">O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="79de8-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="79de8-106">Os cenários de segurança diferem entre o servidor mais incrivelmente e os aplicativos Webassembly de mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="79de8-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="79de8-107">Como os aplicativos de servidor mais amseriais executados no servidor, as verificações de autorização são capazes de determinar:</span><span class="sxs-lookup"><span data-stu-id="79de8-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="79de8-108">As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).</span><span class="sxs-lookup"><span data-stu-id="79de8-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="79de8-109">As regras de acesso para áreas do aplicativo e componentes.</span><span class="sxs-lookup"><span data-stu-id="79de8-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="79de8-110">Aplicativos Webassembly de mais de-vela são executados no cliente.</span><span class="sxs-lookup"><span data-stu-id="79de8-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="79de8-111">A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas.</span><span class="sxs-lookup"><span data-stu-id="79de8-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="79de8-112">Como as verificações do lado do cliente podem ser modificadas ou ignoradas por um usuário, um aplicativo Webassembly de mais alto que não pode impor regras de acesso de autorização.</span><span class="sxs-lookup"><span data-stu-id="79de8-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="79de8-113">Autenticação</span><span class="sxs-lookup"><span data-stu-id="79de8-113">Authentication</span></span>

<span data-ttu-id="79de8-114">O Blazor usa os mecanismos de autenticação do ASP.NET Core existentes para estabelecer a identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="79de8-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="79de8-115">O mecanismo exato depende de como o aplicativo mais incrivelmente é hospedado, um servidor mais incrivelmente ou um Webassembly de mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="79de8-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="79de8-116">Autenticação de servidor mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="79de8-116">Blazor Server authentication</span></span>

<span data-ttu-id="79de8-117">Os aplicativos de servidor mais incrivelmente operam em uma conexão em tempo real que é criada usando o Signalr.</span><span class="sxs-lookup"><span data-stu-id="79de8-117">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="79de8-118">A [autenticação em aplicativos baseados em SignalR](xref:signalr/authn-and-authz) é realizada quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="79de8-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="79de8-119">A autenticação pode ser baseada em um cookie ou um algum outro token de portador.</span><span class="sxs-lookup"><span data-stu-id="79de8-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="79de8-120">O modelo de projeto de servidor mais incrivelmente pode configurar a autenticação para você quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="79de8-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="79de8-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="79de8-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="79de8-122">Siga as diretrizes do Visual Studio no artigo <xref:blazor/get-started> para criar um novo projeto de servidor com um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="79de8-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="79de8-123">Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.</span><span class="sxs-lookup"><span data-stu-id="79de8-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="79de8-124">Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="79de8-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="79de8-125">**Sem Autenticação**</span><span class="sxs-lookup"><span data-stu-id="79de8-125">**No Authentication**</span></span>
* <span data-ttu-id="79de8-126">**Contas de usuário individuais** &ndash; as contas de usuário podem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="79de8-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="79de8-127">Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="79de8-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="79de8-128">Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="79de8-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="79de8-129">**Contas corporativas ou de estudante**</span><span class="sxs-lookup"><span data-stu-id="79de8-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="79de8-130">**Autenticação do Windows**</span><span class="sxs-lookup"><span data-stu-id="79de8-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="79de8-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="79de8-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="79de8-132">Siga as diretrizes de Visual Studio Code no artigo <xref:blazor/get-started> para criar um novo projeto de servidor de nova tentativa com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="79de8-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="79de8-133">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="79de8-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="79de8-134">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="79de8-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="79de8-135">Valor `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="79de8-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="79de8-136">Sem autenticação</span><span class="sxs-lookup"><span data-stu-id="79de8-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="79de8-137">Individual</span><span class="sxs-lookup"><span data-stu-id="79de8-137">Individual</span></span><br><span data-ttu-id="79de8-138">Usuários armazenados no aplicativo com o ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="79de8-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="79de8-139">Individual</span><span class="sxs-lookup"><span data-stu-id="79de8-139">Individual</span></span><br><span data-ttu-id="79de8-140">Usuários armazenados no [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="79de8-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="79de8-141">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="79de8-141">Work or School Accounts</span></span><br><span data-ttu-id="79de8-142">Autenticação organizacional para um único locatário.</span><span class="sxs-lookup"><span data-stu-id="79de8-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="79de8-143">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="79de8-143">Work or School Accounts</span></span><br><span data-ttu-id="79de8-144">Autenticação organizacional para vários locatários.</span><span class="sxs-lookup"><span data-stu-id="79de8-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="79de8-145">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="79de8-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="79de8-146">O comando cria uma pasta com nome do valor fornecido para o espaço reservado `{APP NAME}` e usa o nome da pasta como o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79de8-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="79de8-147">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="79de8-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

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

### <a name="blazor-webassembly-authentication"></a><span data-ttu-id="79de8-148">Autenticação Webassembly mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="79de8-148">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="79de8-149">Em aplicativos Webassembly mais poseriais, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado pelos usuários.</span><span class="sxs-lookup"><span data-stu-id="79de8-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="79de8-150">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="79de8-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="79de8-151">Adicione uma referência de pacote para [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) ao arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="79de8-151">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="79de8-152">A implementação de um serviço personalizado `AuthenticationStateProvider` para aplicativos Webassembly mais incrivelmente é abordada nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="79de8-152">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="79de8-153">Serviço AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="79de8-153">AuthenticationStateProvider service</span></span>

<span data-ttu-id="79de8-154">Os aplicativos de servidor mais elaborados incluem um serviço interno `AuthenticationStateProvider` que obtém dados de estado de autenticação do @no__t de ASP.NET Core-1.</span><span class="sxs-lookup"><span data-stu-id="79de8-154">Blazor Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="79de8-155">Essa é a maneira que o estado de autenticação se integra a mecanismos de autenticação existentes no lado do servidor do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="79de8-155">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="79de8-156">O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="79de8-156">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="79de8-157">Normalmente, você não usa o `AuthenticationStateProvider` diretamente.</span><span class="sxs-lookup"><span data-stu-id="79de8-157">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="79de8-158">Use as abordagens do [componente AuthorizeView](#authorizeview-component) ou [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) descritas mais adiante neste artigo.</span><span class="sxs-lookup"><span data-stu-id="79de8-158">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="79de8-159">A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.</span><span class="sxs-lookup"><span data-stu-id="79de8-159">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="79de8-160">O serviço `AuthenticationStateProvider` pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="79de8-160">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```cshtml
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
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

<span data-ttu-id="79de8-161">Se `user.Identity.IsAuthenticated` for `true` e como o usuário é um <xref:System.Security.Claims.ClaimsPrincipal>, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="79de8-161">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="79de8-162">Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="79de8-162">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="79de8-163">Implementar um AuthenticationStateProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="79de8-163">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="79de8-164">Se você estiver criando um aplicativo Webassembly mais elaborado ou se a especificação do seu aplicativo precisar absolutamente de um provedor personalizado, implemente um provedor e substitua `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="79de8-164">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="79de8-165">O serviço `CustomAuthStateProvider` é registrado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="79de8-165">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Components.Authorization;
// using BlazorSample.Services;

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="79de8-166">Ao usarem o `CustomAuthStateProvider`, todos os usuários são autenticados com o nome de usuário `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="79de8-166">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="79de8-167">Expor o estado de autenticação como um parâmetro em cascata</span><span class="sxs-lookup"><span data-stu-id="79de8-167">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="79de8-168">Se os dados do estado de autenticação forem necessários para a lógica do procedimento, como ao realizar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="79de8-168">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

> [!NOTE]
> <span data-ttu-id="79de8-169">Em um componente de aplicativo Webassembly mais incrivelmente, adicione o namespace `Microsoft.AspNetCore.Components.Authorization` (`@using Microsoft.AspNetCore.Components.Authorization`).</span><span class="sxs-lookup"><span data-stu-id="79de8-169">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="79de8-170">Se `user.Identity.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="79de8-170">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="79de8-171">Configure o parâmetro em cascata `Task<AuthenticationState>` usando os componentes `AuthorizeRouteView` e `CascadingAuthenticationState`:</span><span class="sxs-lookup"><span data-stu-id="79de8-171">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components:</span></span>

```cshtml
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

## <a name="authorization"></a><span data-ttu-id="79de8-172">Autorização</span><span class="sxs-lookup"><span data-stu-id="79de8-172">Authorization</span></span>

<span data-ttu-id="79de8-173">Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.</span><span class="sxs-lookup"><span data-stu-id="79de8-173">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="79de8-174">O acesso geralmente é concedido ou negado com base nos seguintes casos:</span><span class="sxs-lookup"><span data-stu-id="79de8-174">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="79de8-175">Se o usuário está autenticado (conectado).</span><span class="sxs-lookup"><span data-stu-id="79de8-175">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="79de8-176">Se o usuário está em uma *função*.</span><span class="sxs-lookup"><span data-stu-id="79de8-176">A user is in a *role*.</span></span>
* <span data-ttu-id="79de8-177">Se o usuário tem uma *declaração*.</span><span class="sxs-lookup"><span data-stu-id="79de8-177">A user has a *claim*.</span></span>
* <span data-ttu-id="79de8-178">Se uma *política* é atendida.</span><span class="sxs-lookup"><span data-stu-id="79de8-178">A *policy* is satisfied.</span></span>

<span data-ttu-id="79de8-179">Todos esses conceitos são iguais no MVC do ASP.NET Core ou em aplicativos Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="79de8-179">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="79de8-180">Para obter mais informações sobre a segurança do ASP.NET Core, confira os artigos em [Identidade e segurança do ASP.NET Core](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="79de8-180">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="79de8-181">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="79de8-181">AuthorizeView component</span></span>

<span data-ttu-id="79de8-182">O componente `AuthorizeView` exibe de forma seletiva a interface do usuário, caso o usuário esteja autorizado a vê-la.</span><span class="sxs-lookup"><span data-stu-id="79de8-182">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="79de8-183">Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.</span><span class="sxs-lookup"><span data-stu-id="79de8-183">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="79de8-184">O componente expõe uma variável `context` do tipo `AuthenticationState`, que pode ser usada para acessar informações sobre o usuário conectado:</span><span class="sxs-lookup"><span data-stu-id="79de8-184">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="79de8-185">Também é possível fornecer um conteúdo diferente para ser exibido caso o usuário não esteja autenticado:</span><span class="sxs-lookup"><span data-stu-id="79de8-185">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="79de8-186">O conteúdo das marcas `<Authorized>` e `<NotAuthorized>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="79de8-186">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="79de8-187">As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).</span><span class="sxs-lookup"><span data-stu-id="79de8-187">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="79de8-188">Se as condições de autorização não forem especificadas, o `AuthorizeView` usará uma política padrão e tratará:</span><span class="sxs-lookup"><span data-stu-id="79de8-188">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="79de8-189">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="79de8-189">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="79de8-190">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="79de8-190">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="79de8-191">Autorização baseada em funções e em políticas</span><span class="sxs-lookup"><span data-stu-id="79de8-191">Role-based and policy-based authorization</span></span>

<span data-ttu-id="79de8-192">O componente `AuthorizeView` dá suporte à autorização *baseada em funções* ou *baseada em políticas*.</span><span class="sxs-lookup"><span data-stu-id="79de8-192">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="79de8-193">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="79de8-193">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="79de8-194">Para obter mais informações, consulte <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="79de8-194">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="79de8-195">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="79de8-195">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="79de8-196">A autorização baseada em declarações é um caso especial de autorização baseada em políticas.</span><span class="sxs-lookup"><span data-stu-id="79de8-196">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="79de8-197">Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração.</span><span class="sxs-lookup"><span data-stu-id="79de8-197">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="79de8-198">Para obter mais informações, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="79de8-198">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="79de8-199">Essas APIs podem ser usadas no servidor de um ou mais aplicativos Webassembly de mais ou mais.</span><span class="sxs-lookup"><span data-stu-id="79de8-199">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="79de8-200">Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.</span><span class="sxs-lookup"><span data-stu-id="79de8-200">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="79de8-201">Conteúdo exibido durante a autenticação assíncrona</span><span class="sxs-lookup"><span data-stu-id="79de8-201">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="79de8-202">O Blazor permite que o estado de autenticação seja determinado *assincronamente*.</span><span class="sxs-lookup"><span data-stu-id="79de8-202">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="79de8-203">O cenário principal para essa abordagem é em aplicativos Webassembly mais podestas que fazem uma solicitação para um ponto de extremidade externo para autenticação.</span><span class="sxs-lookup"><span data-stu-id="79de8-203">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="79de8-204">Enquanto a autenticação estiver em andamento, `AuthorizeView` não exibirá nenhum conteúdo por padrão.</span><span class="sxs-lookup"><span data-stu-id="79de8-204">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="79de8-205">Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="79de8-205">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="79de8-206">Essa abordagem não é normalmente aplicável a aplicativos de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="79de8-206">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="79de8-207">Os aplicativos de servidor mais incrivelmente sabem o estado de autenticação assim que o estado é estabelecido.</span><span class="sxs-lookup"><span data-stu-id="79de8-207">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="79de8-208">o conteúdo `Authorizing` pode ser fornecido no componente `AuthorizeView` de um aplicativo de servidor mais incrivelmente, mas o conteúdo nunca é exibido.</span><span class="sxs-lookup"><span data-stu-id="79de8-208">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="79de8-209">Atributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="79de8-209">[Authorize] attribute</span></span>

<span data-ttu-id="79de8-210">O atributo `[Authorize]` pode ser usado em componentes do Razor:</span><span class="sxs-lookup"><span data-stu-id="79de8-210">The `[Authorize]` attribute can be used in Razor components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="79de8-211">Em um componente de aplicativo Webassembly mais incrivelmente, adicione o namespace `Microsoft.AspNetCore.Authorization` (`@using Microsoft.AspNetCore.Authorization`) aos exemplos nesta seção.</span><span class="sxs-lookup"><span data-stu-id="79de8-211">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="79de8-212">Use `[Authorize]` somente em componentes `@page` acessados por meio do roteador Blazor.</span><span class="sxs-lookup"><span data-stu-id="79de8-212">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="79de8-213">A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página.</span><span class="sxs-lookup"><span data-stu-id="79de8-213">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="79de8-214">Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="79de8-214">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="79de8-215">O atributo `[Authorize]` também dá suporte à autorização baseada em funções ou em políticas.</span><span class="sxs-lookup"><span data-stu-id="79de8-215">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="79de8-216">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="79de8-216">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="79de8-217">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="79de8-217">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="79de8-218">Se `Roles` e `Policy` não forem especificados, `[Authorize]` usará a política padrão, que tratará:</span><span class="sxs-lookup"><span data-stu-id="79de8-218">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="79de8-219">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="79de8-219">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="79de8-220">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="79de8-220">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="79de8-221">Personalizar conteúdo não autorizado com o componente Router</span><span class="sxs-lookup"><span data-stu-id="79de8-221">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="79de8-222">O componente `Router`, em conjunto com o componente `AuthorizeRouteView`, permite que o aplicativo especifique conteúdo personalizado se:</span><span class="sxs-lookup"><span data-stu-id="79de8-222">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="79de8-223">O conteúdo não for encontrado.</span><span class="sxs-lookup"><span data-stu-id="79de8-223">Content isn't found.</span></span>
* <span data-ttu-id="79de8-224">O usuário não atender à condição `[Authorize]` aplicada ao componente.</span><span class="sxs-lookup"><span data-stu-id="79de8-224">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="79de8-225">O atributo `[Authorize]` é abordado na seção [Atributo [Authorize]](#authorize-attribute).</span><span class="sxs-lookup"><span data-stu-id="79de8-225">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="79de8-226">A autenticação assíncrona estiver em andamento.</span><span class="sxs-lookup"><span data-stu-id="79de8-226">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="79de8-227">No modelo de projeto de servidor padrão, o arquivo *app. Razor* demonstra como definir o conteúdo personalizado:</span><span class="sxs-lookup"><span data-stu-id="79de8-227">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```cshtml
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

<span data-ttu-id="79de8-228">O conteúdo das marcas `<NotFound>`, `<NotAuthorized>` e `<Authorizing>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="79de8-228">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="79de8-229">Se o elemento `<NotAuthorized>` não for especificado, o `AuthorizeRouteView` usará a seguinte mensagem de fallback:</span><span class="sxs-lookup"><span data-stu-id="79de8-229">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="79de8-230">Notificação sobre mudanças no estado de autenticação</span><span class="sxs-lookup"><span data-stu-id="79de8-230">Notification about authentication state changes</span></span>

<span data-ttu-id="79de8-231">Se o aplicativo determinar que os dados sobre o estado de autenticação subjacente foram alterados (por exemplo, porque o usuário se desconectou ou porque outro usuário alterou suas funções), um `AuthenticationStateProvider` personalizado poderá invocar opcionalmente o método `NotifyAuthenticationStateChanged` na classe base `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="79de8-231">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="79de8-232">Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.</span><span class="sxs-lookup"><span data-stu-id="79de8-232">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="79de8-233">Lógica de procedimento</span><span class="sxs-lookup"><span data-stu-id="79de8-233">Procedural logic</span></span>

<span data-ttu-id="79de8-234">Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário.</span><span class="sxs-lookup"><span data-stu-id="79de8-234">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="79de8-235">O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.</span><span class="sxs-lookup"><span data-stu-id="79de8-235">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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

> [!NOTE]
> <span data-ttu-id="79de8-236">Em um componente de aplicativo Webassembly mais incrivelmente, adicione os namespaces `Microsoft.AspNetCore.Authorization` e `Microsoft.AspNetCore.Components.Authorization`:</span><span class="sxs-lookup"><span data-stu-id="79de8-236">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```cshtml
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="79de8-237">Autorização em aplicativos Webassembly de mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="79de8-237">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="79de8-238">Em aplicativos Webassembly mais poseriais, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado pelos usuários.</span><span class="sxs-lookup"><span data-stu-id="79de8-238">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="79de8-239">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="79de8-239">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="79de8-240">**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**</span><span class="sxs-lookup"><span data-stu-id="79de8-240">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="79de8-241">Solucionar erros</span><span class="sxs-lookup"><span data-stu-id="79de8-241">Troubleshoot errors</span></span>

<span data-ttu-id="79de8-242">Erros comuns:</span><span class="sxs-lookup"><span data-stu-id="79de8-242">Common errors:</span></span>

* <span data-ttu-id="79de8-243">**A autorização requer um parâmetro em cascata do tipo Task @ no__t-1AuthenticationState >. Considere o uso de CascadingAuthenticationState para fornecer isso.**</span><span class="sxs-lookup"><span data-stu-id="79de8-243">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="79de8-244">**O valor `null` é recebido para `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="79de8-244">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="79de8-245">É provável que o projeto não tenha sido criado usando um modelo de servidor mais incrivelmente com a autenticação habilitada.</span><span class="sxs-lookup"><span data-stu-id="79de8-245">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="79de8-246">Encapsule um `<CascadingAuthenticationState>` em torno de alguma parte da árvore da interface do usuário, por exemplo, em *App.razor*, da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="79de8-246">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="79de8-247">O `CascadingAuthenticationState` fornece o parâmetro em cascata `Task<AuthenticationState>` que, por sua vez, ele recebe do serviço DI subjacente `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="79de8-247">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79de8-248">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="79de8-248">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
