---
title: Autenticação e autorização do ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre os cenários de autenticação e autorização do Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: ba00c0370094df4e0ae0479aeb23eceea6ef04f0
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083342"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="2cdd4-103">Autorização e autenticação no Blazor em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cdd4-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="2cdd4-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="2cdd4-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2cdd4-105">O ASP.NET Core dá suporte à configuração e ao gerenciamento de segurança em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="2cdd4-106">Os cenários de segurança diferem entre o servidor mais incrivelmente e os aplicativos Webassembly de mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="2cdd4-107">Como os aplicativos de servidor mais amseriais executados no servidor, as verificações de autorização são capazes de determinar:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="2cdd4-108">As opções de interface do usuário apresentadas ao usuário (por exemplo, as entradas de menu disponíveis a um usuário).</span><span class="sxs-lookup"><span data-stu-id="2cdd4-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="2cdd4-109">As regras de acesso para áreas do aplicativo e componentes.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="2cdd4-110">Aplicativos Webassembly de mais de-vela são executados no cliente.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="2cdd4-111">A autorização é *somente* usada para determinar quais opções da interface do usuário serão apresentadas.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="2cdd4-112">Como as verificações do lado do cliente podem ser modificadas ou ignoradas por um usuário, um aplicativo Webassembly de mais alto que não pode impor regras de acesso de autorização.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="2cdd4-113">Autenticação</span><span class="sxs-lookup"><span data-stu-id="2cdd4-113">Authentication</span></span>

<span data-ttu-id="2cdd4-114">O Blazor usa os mecanismos de autenticação do ASP.NET Core existentes para estabelecer a identidade do usuário.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="2cdd4-115">O mecanismo exato depende de como o aplicativo mais incrivelmente é hospedado, um servidor mais incrivelmente ou um Webassembly de mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="2cdd4-116">Autenticação de servidor mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="2cdd4-116">Blazor Server authentication</span></span>

<span data-ttu-id="2cdd4-117">Os aplicativos de servidor mais incrivelmente operam em uma conexão em tempo real que é criada usando o Signalr.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-117">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="2cdd4-118">A [autenticação em aplicativos baseados em SignalR](xref:signalr/authn-and-authz) é realizada quando a conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="2cdd4-119">A autenticação pode ser baseada em um cookie ou um algum outro token de portador.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="2cdd4-120">O modelo de projeto de servidor mais incrivelmente pode configurar a autenticação para você quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2cdd4-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cdd4-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2cdd4-122">Siga as diretrizes do Visual Studio no artigo de <xref:blazor/get-started> para criar um novo projeto de servidor com um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="2cdd4-123">Depois de escolher o modelo **Blazor Server App** na caixa de diálogo **Criar um novo aplicativo Web ASP.NET Core**, selecione **Alterar** em **Autenticação**.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="2cdd4-124">Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="2cdd4-125">**Sem Autenticação**</span><span class="sxs-lookup"><span data-stu-id="2cdd4-125">**No Authentication**</span></span>
* <span data-ttu-id="2cdd4-126">**Contas de usuário individuais** &ndash; contas de usuário podem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="2cdd4-127">Dentro do aplicativo usando o sistema de [Identidade](xref:security/authentication/identity) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="2cdd4-128">Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="2cdd4-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="2cdd4-129">**Contas corporativas ou de estudante**</span><span class="sxs-lookup"><span data-stu-id="2cdd4-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="2cdd4-130">**Autenticação do Windows**</span><span class="sxs-lookup"><span data-stu-id="2cdd4-130">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2cdd4-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2cdd4-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2cdd4-132">Siga as diretrizes de Visual Studio Code no artigo de <xref:blazor/get-started> para criar um novo projeto de servidor com um mecanismo de autenticação:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="2cdd4-133">Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="2cdd4-134">Mecanismo de autenticação</span><span class="sxs-lookup"><span data-stu-id="2cdd4-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="2cdd4-135">`{AUTHENTICATION}` valor</span><span class="sxs-lookup"><span data-stu-id="2cdd4-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="2cdd4-136">Sem Autenticação</span><span class="sxs-lookup"><span data-stu-id="2cdd4-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="2cdd4-137">Individual</span><span class="sxs-lookup"><span data-stu-id="2cdd4-137">Individual</span></span><br><span data-ttu-id="2cdd4-138">Usuários armazenados no aplicativo com o ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="2cdd4-139">Individual</span><span class="sxs-lookup"><span data-stu-id="2cdd4-139">Individual</span></span><br><span data-ttu-id="2cdd4-140">Usuários armazenados no [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="2cdd4-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="2cdd4-141">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="2cdd4-141">Work or School Accounts</span></span><br><span data-ttu-id="2cdd4-142">Autenticação organizacional para um único locatário.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="2cdd4-143">Contas corporativas ou de estudante</span><span class="sxs-lookup"><span data-stu-id="2cdd4-143">Work or School Accounts</span></span><br><span data-ttu-id="2cdd4-144">Autenticação organizacional para vários locatários.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="2cdd4-145">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="2cdd4-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="2cdd4-146">O comando cria uma pasta com nome do valor fornecido para o espaço reservado `{APP NAME}` e usa o nome da pasta como o nome do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="2cdd4-147">Para obter mais informações, confira o [novo comando do dotnet](/dotnet/core/tools/dotnet-new) no Guia do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

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

### <a name="opno-locblazor-webassembly-authentication"></a><span data-ttu-id="2cdd4-148">autenticação de Webassembly Blazor</span><span class="sxs-lookup"><span data-stu-id="2cdd4-148">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="2cdd4-149">Em Blazor aplicativos Webassembly, as verificações de autenticação podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="2cdd4-150">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="2cdd4-151">Adicione uma referência de pacote para [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) ao arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-151">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="2cdd4-152">A implementação de um serviço de `AuthenticationStateProvider` personalizado para aplicativos Webassembly Blazor é abordada nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-152">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="2cdd4-153">Serviço AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="2cdd4-153">AuthenticationStateProvider service</span></span>

<span data-ttu-id="2cdd4-154">os aplicativos do Blazor Server incluem um serviço de `AuthenticationStateProvider` interno que obtém dados de estado de autenticação do `HttpContext.User`de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-154">Blazor Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="2cdd4-155">Essa é a maneira que o estado de autenticação se integra a mecanismos de autenticação existentes no lado do servidor do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-155">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="2cdd4-156">O `AuthenticationStateProvider` é o serviço subjacente usado pelos componentes `AuthorizeView` e `CascadingAuthenticationState` para obter o estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-156">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="2cdd4-157">Normalmente, você não usa o `AuthenticationStateProvider` diretamente.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-157">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="2cdd4-158">Use as abordagens do [componente AuthorizeView](#authorizeview-component) ou [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) descritas mais adiante neste artigo.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-158">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="2cdd4-159">A principal desvantagem de usar o `AuthenticationStateProvider` diretamente é que o componente não será notificado automaticamente se os dados subjacentes do estado de autenticação forem alterados.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-159">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="2cdd4-160">O serviço `AuthenticationStateProvider` pode fornecer os dados de <xref:System.Security.Claims.ClaimsPrincipal> do usuário atual, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-160">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="2cdd4-161">Se `user.Identity.IsAuthenticated` for `true` e como o usuário é um <xref:System.Security.Claims.ClaimsPrincipal>, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-161">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="2cdd4-162">Para obter mais informações sobre a DI (injeção de dependência) e os serviços, confira <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-162">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="2cdd4-163">Implementar um AuthenticationStateProvider personalizado</span><span class="sxs-lookup"><span data-stu-id="2cdd4-163">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="2cdd4-164">Se você estiver criando um aplicativo Webassembly Blazor ou se a especificação do seu aplicativo for absolutamente necessária um provedor personalizado, implemente um provedor e substitua `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-164">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="2cdd4-165">Em um aplicativo Webassembly Blazor, o serviço `CustomAuthStateProvider` é registrado em `Main` do *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-165">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

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

<span data-ttu-id="2cdd4-166">Ao usarem o `CustomAuthStateProvider`, todos os usuários são autenticados com o nome de usuário `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-166">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="2cdd4-167">Expor o estado de autenticação como um parâmetro em cascata</span><span class="sxs-lookup"><span data-stu-id="2cdd4-167">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="2cdd4-168">Se os dados do estado de autenticação forem necessários para a lógica do procedimento, como ao realizar uma ação disparada pelo usuário, obtenha os dados de estado de autenticação definindo um parâmetro em cascata do tipo `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-168">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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
> <span data-ttu-id="2cdd4-169">Em um componente de aplicativo Webassembly Blazor, adicione o namespace `Microsoft.AspNetCore.Components.Authorization` (`@using Microsoft.AspNetCore.Components.Authorization`).</span><span class="sxs-lookup"><span data-stu-id="2cdd4-169">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="2cdd4-170">Se `user.Identity.IsAuthenticated` for `true`, será possível enumerar as declarações e avaliar a associação nas funções.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-170">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="2cdd4-171">Configure o `Task<AuthenticationState>` parâmetro em cascata usando os componentes `AuthorizeRouteView` e `CascadingAuthenticationState` no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="2cdd4-171">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the *App.razor* file:</span></span>

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

<span data-ttu-id="2cdd4-172">Adicionar serviços para opções e autorização para `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-172">Add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

## <a name="authorization"></a><span data-ttu-id="2cdd4-173">Autorização</span><span class="sxs-lookup"><span data-stu-id="2cdd4-173">Authorization</span></span>

<span data-ttu-id="2cdd4-174">Depois que o usuário é autenticado, as regras de *autorização* são aplicadas para controlar o que ele poderá fazer.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-174">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="2cdd4-175">O acesso geralmente é concedido ou negado com base nos seguintes casos:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-175">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="2cdd4-176">Se o usuário está autenticado (conectado).</span><span class="sxs-lookup"><span data-stu-id="2cdd4-176">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="2cdd4-177">Se o usuário está em uma *função*.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-177">A user is in a *role*.</span></span>
* <span data-ttu-id="2cdd4-178">Se o usuário tem uma *declaração*.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-178">A user has a *claim*.</span></span>
* <span data-ttu-id="2cdd4-179">Se uma *política* é atendida.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-179">A *policy* is satisfied.</span></span>

<span data-ttu-id="2cdd4-180">Todos esses conceitos são iguais no MVC do ASP.NET Core ou em aplicativos Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-180">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="2cdd4-181">Para obter mais informações sobre a segurança do ASP.NET Core, confira os artigos em [Identidade e segurança do ASP.NET Core](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="2cdd4-181">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="2cdd4-182">Componente AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="2cdd4-182">AuthorizeView component</span></span>

<span data-ttu-id="2cdd4-183">O componente `AuthorizeView` exibe de forma seletiva a interface do usuário, caso o usuário esteja autorizado a vê-la.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-183">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="2cdd4-184">Essa abordagem é útil quando você precisa apenas *exibir* dados para o usuário e não precisa usar a identidade dele na lógica de procedimento.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-184">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="2cdd4-185">O componente expõe uma variável `context` do tipo `AuthenticationState`, que pode ser usada para acessar informações sobre o usuário conectado:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-185">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="2cdd4-186">Também é possível fornecer um conteúdo diferente para ser exibido caso o usuário não esteja autenticado:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-186">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="2cdd4-187">O conteúdo das marcas de `<Authorized>` e `<NotAuthorized>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-187">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="2cdd4-188">As condições de autorização, como funções ou políticas que controlam o acesso ou as opções da interface do usuário, são abordadas na seção [Autorização](#authorization).</span><span class="sxs-lookup"><span data-stu-id="2cdd4-188">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="2cdd4-189">Se as condições de autorização não forem especificadas, o `AuthorizeView` usará uma política padrão e tratará:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-189">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="2cdd4-190">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-190">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="2cdd4-191">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-191">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="2cdd4-192">Autorização baseada em funções e em políticas</span><span class="sxs-lookup"><span data-stu-id="2cdd4-192">Role-based and policy-based authorization</span></span>

<span data-ttu-id="2cdd4-193">O componente `AuthorizeView` dá suporte à autorização *baseada em funções* ou *baseada em políticas*.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-193">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="2cdd4-194">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-194">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="2cdd4-195">Para obter mais informações, consulte <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-195">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="2cdd4-196">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-196">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="2cdd4-197">A autorização baseada em declarações é um caso especial de autorização baseada em políticas.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-197">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="2cdd4-198">Por exemplo, você pode definir uma política que exige que os usuários tenham determinada declaração.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-198">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="2cdd4-199">Para obter mais informações, consulte <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-199">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="2cdd4-200">Essas APIs podem ser usadas em um servidor Blazor ou Blazor aplicativos Webassembly.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-200">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="2cdd4-201">Se `Roles` e `Policy` não forem especificados, o `AuthorizeView` usará a política padrão.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-201">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="2cdd4-202">Conteúdo exibido durante a autenticação assíncrona</span><span class="sxs-lookup"><span data-stu-id="2cdd4-202">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="2cdd4-203"> permite que o estado de autenticação seja determinado de *forma assíncrona*.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-203"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="2cdd4-204">O cenário principal para essa abordagem é em Blazor aplicativos Webassembly que fazem uma solicitação para um ponto de extremidade externo para autenticação.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-204">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="2cdd4-205">Enquanto a autenticação estiver em andamento, `AuthorizeView` não exibirá nenhum conteúdo por padrão.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-205">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="2cdd4-206">Para exibir o conteúdo enquanto a autenticação ocorre, use o elemento `<Authorizing>`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-206">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="2cdd4-207">Essa abordagem normalmente não é aplicável a aplicativos Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-207">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="2cdd4-208">os aplicativos do Blazor Server conhecem o estado de autenticação assim que o estado é estabelecido.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-208">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="2cdd4-209">`Authorizing` conteúdo pode ser fornecido em um componente de `AuthorizeView` do aplicativo Blazor Server, mas o conteúdo nunca é exibido.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-209">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="2cdd4-210">Atributo [Authorize]</span><span class="sxs-lookup"><span data-stu-id="2cdd4-210">[Authorize] attribute</span></span>

<span data-ttu-id="2cdd4-211">O atributo `[Authorize]` pode ser usado em componentes do Razor:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-211">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="2cdd4-212">Em um componente de aplicativo Webassembly Blazor, adicione o namespace `Microsoft.AspNetCore.Authorization` (`@using Microsoft.AspNetCore.Authorization`) aos exemplos nesta seção.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-212">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2cdd4-213">Use apenas `[Authorize]` em componentes `@page` atingidos por meio do roteador Blazor.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-213">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="2cdd4-214">A autorização é realizada apenas como um aspecto do roteamento e *não* para componentes filho renderizados dentro de uma página.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-214">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="2cdd4-215">Para autorizar a exibição de partes específicas dentro de uma página, use `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-215">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="2cdd4-216">O atributo `[Authorize]` também dá suporte à autorização baseada em funções ou em políticas.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-216">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="2cdd4-217">Para a autorização baseada em funções, use o parâmetro `Roles`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-217">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="2cdd4-218">Para a autorização baseada em políticas, use o parâmetro `Policy`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-218">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="2cdd4-219">Se `Roles` e `Policy` não forem especificados, `[Authorize]` usará a política padrão, que tratará:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-219">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="2cdd4-220">Usuários autenticados (conectados) como autorizados.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-220">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="2cdd4-221">Usuários não autenticados (não conectados) como não autorizados.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-221">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="2cdd4-222">Personalizar conteúdo não autorizado com o componente Router</span><span class="sxs-lookup"><span data-stu-id="2cdd4-222">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="2cdd4-223">O componente `Router`, em conjunto com o componente `AuthorizeRouteView`, permite que o aplicativo especifique conteúdo personalizado se:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-223">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="2cdd4-224">O conteúdo não for encontrado.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-224">Content isn't found.</span></span>
* <span data-ttu-id="2cdd4-225">O usuário não atender à condição `[Authorize]` aplicada ao componente.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-225">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="2cdd4-226">O atributo `[Authorize]` é abordado na seção [atributo de`[Authorize]`](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="2cdd4-226">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="2cdd4-227">A autenticação assíncrona estiver em andamento.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-227">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="2cdd4-228">No modelo de projeto padrão do Blazor Server, o arquivo *app. Razor* demonstra como definir o conteúdo personalizado:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-228">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="2cdd4-229">O conteúdo das marcas `<NotFound>`, `<NotAuthorized>`e `<Authorizing>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-229">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="2cdd4-230">Se o elemento `<NotAuthorized>` não for especificado, o `AuthorizeRouteView` usará a seguinte mensagem de fallback:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-230">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="2cdd4-231">Notificação sobre mudanças no estado de autenticação</span><span class="sxs-lookup"><span data-stu-id="2cdd4-231">Notification about authentication state changes</span></span>

<span data-ttu-id="2cdd4-232">Se o aplicativo determinar que os dados sobre o estado de autenticação subjacente foram alterados (por exemplo, porque o usuário se desconectou ou porque outro usuário alterou suas funções), um `AuthenticationStateProvider` personalizado poderá invocar opcionalmente o método `NotifyAuthenticationStateChanged` na classe base `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-232">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="2cdd4-233">Isso notificará os consumidores a respeito dos dados de estado de autenticação (por exemplo, `AuthorizeView`) para realizar uma nova renderização usando os novos dados.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-233">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="2cdd4-234">Lógica de procedimento</span><span class="sxs-lookup"><span data-stu-id="2cdd4-234">Procedural logic</span></span>

<span data-ttu-id="2cdd4-235">Se for necessário que o aplicativo verifique as regras de autorização como parte da lógica de procedimento, use um parâmetro em cascata do tipo `Task<AuthenticationState>` para obter o <xref:System.Security.Claims.ClaimsPrincipal> do usuário.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-235">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2cdd4-236">O `Task<AuthenticationState>` pode ser combinado com outros serviços, como `IAuthorizationService`, para avaliar as políticas.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-236">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="2cdd4-237">Em um componente de aplicativo Webassembly Blazor, adicione os namespaces de `Microsoft.AspNetCore.Authorization` e `Microsoft.AspNetCore.Components.Authorization`:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-237">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="2cdd4-238">Autorização em aplicativos Webassembly Blazor</span><span class="sxs-lookup"><span data-stu-id="2cdd4-238">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="2cdd4-239">Em Blazor aplicativos Webassembly, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-239">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="2cdd4-240">Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-240">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="2cdd4-241">**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**</span><span class="sxs-lookup"><span data-stu-id="2cdd4-241">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="2cdd4-242">Para obter mais informações, consulte os artigos em <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-242">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="2cdd4-243">Solucionar problemas de erros</span><span class="sxs-lookup"><span data-stu-id="2cdd4-243">Troubleshoot errors</span></span>

<span data-ttu-id="2cdd4-244">Erros comuns:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-244">Common errors:</span></span>

* <span data-ttu-id="2cdd4-245">**A autorização requer um parâmetro em cascata do tipo Task\<Authenticationstate >. Considere o uso de CascadingAuthenticationState para fornecer isso.**</span><span class="sxs-lookup"><span data-stu-id="2cdd4-245">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="2cdd4-246">**O valor `null` é recebido para `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="2cdd4-246">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="2cdd4-247">É provável que o projeto não tenha sido criado usando um modelo de servidor Blazor com autenticação habilitada.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-247">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="2cdd4-248">Encapsule um `<CascadingAuthenticationState>` em torno de alguma parte da árvore da interface do usuário, por exemplo, em *App.razor*, da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="2cdd4-248">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="2cdd4-249">O `CascadingAuthenticationState` fornece o parâmetro em cascata `Task<AuthenticationState>` que, por sua vez, ele recebe do serviço DI subjacente `AuthenticationStateProvider`.</span><span class="sxs-lookup"><span data-stu-id="2cdd4-249">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cdd4-250">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2cdd4-250">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="2cdd4-251">[Awesome Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) links de exemplo da comunidade de autenticação</span><span class="sxs-lookup"><span data-stu-id="2cdd4-251">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
