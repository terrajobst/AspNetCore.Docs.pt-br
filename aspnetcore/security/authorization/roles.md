---
title: Autorização baseada em função no ASP.NET Core
author: rick-anderson
description: Aprenda a restringir o acesso de controlador e ação do ASP.NET Core, passando as funções para o atributo Authorize.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 0467ea82831bffe6882e584930c2fa1212a244c7
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248089"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="db0ea-103">Autorização baseada em função no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db0ea-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="db0ea-104">Quando uma identidade é criada ele pode pertencer a uma ou mais funções.</span><span class="sxs-lookup"><span data-stu-id="db0ea-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="db0ea-105">Por exemplo, Tracy pode pertencer às funções de administrador e usuário, embora Scott só pode pertencer à função de usuário.</span><span class="sxs-lookup"><span data-stu-id="db0ea-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="db0ea-106">Como essas funções são criadas e gerenciadas dependem do repositório de backup do processo de autorização.</span><span class="sxs-lookup"><span data-stu-id="db0ea-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="db0ea-107">As funções são expostas para o desenvolvedor por meio de [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) método na [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) classe.</span><span class="sxs-lookup"><span data-stu-id="db0ea-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

::: moniker range=">= aspnetcore-2.0"

> [!IMPORTANT]
> <span data-ttu-id="db0ea-108">Este tópico **não** se aplica a Páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="db0ea-108">This topic does **not** apply to Razor Pages.</span></span> <span data-ttu-id="db0ea-109">Dá suporte a páginas do Razor [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter).</span><span class="sxs-lookup"><span data-stu-id="db0ea-109">Razor Pages supports [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter).</span></span> <span data-ttu-id="db0ea-110">Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="db0ea-110">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

::: moniker-end

## <a name="adding-role-checks"></a><span data-ttu-id="db0ea-111">Adicionar verificações de função</span><span class="sxs-lookup"><span data-stu-id="db0ea-111">Adding role checks</span></span>

<span data-ttu-id="db0ea-112">Verificações de autorização baseado em função são declarativas&mdash;o desenvolvedor incorpora-los dentro de seu código, em relação a um controlador ou uma ação dentro de um controlador, especificando as funções que o usuário atual deve ser um membro de acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="db0ea-112">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="db0ea-113">Por exemplo, o código a seguir limita o acesso a quaisquer ações sobre o `AdministrationController` aos usuários que são membros do `Administrator` função:</span><span class="sxs-lookup"><span data-stu-id="db0ea-113">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="db0ea-114">Você pode especificar várias funções como uma lista separada por vírgulas:</span><span class="sxs-lookup"><span data-stu-id="db0ea-114">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="db0ea-115">Esse controlador seria apenas ser acessado por usuários que são membros do `HRManager` função ou o `Finance` função.</span><span class="sxs-lookup"><span data-stu-id="db0ea-115">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="db0ea-116">Se você aplicar vários atributos de um usuário ao acessar deve ser um membro de todas as funções especificadas; o exemplo a seguir requer que um usuário deve ser um membro de ambos os `PowerUser` e `ControlPanelUser` função.</span><span class="sxs-lookup"><span data-stu-id="db0ea-116">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="db0ea-117">Você pode limitar o acesso ainda mais aplicando atributos de autorização de função adicionais em nível de ação:</span><span class="sxs-lookup"><span data-stu-id="db0ea-117">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="db0ea-118">Nos membros de trecho de código anterior do `Administrator` função ou o `PowerUser` função pode acessar o controlador e o `SetTime` ação, mas somente os membros dos `Administrator` função pode acessar o `ShutDown` ação.</span><span class="sxs-lookup"><span data-stu-id="db0ea-118">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="db0ea-119">Você também pode bloquear um controlador mas permitir o acesso anônimo, não autenticado a ações individuais.</span><span class="sxs-lookup"><span data-stu-id="db0ea-119">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="db0ea-120">Verificações de função baseada em política</span><span class="sxs-lookup"><span data-stu-id="db0ea-120">Policy based role checks</span></span>

<span data-ttu-id="db0ea-121">Requisitos da função também podem ser expressos usando a nova sintaxe de diretiva, em que um desenvolvedor registra uma política na inicialização como parte da configuração do serviço de autorização.</span><span class="sxs-lookup"><span data-stu-id="db0ea-121">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="db0ea-122">Isso normalmente ocorre `ConfigureServices()` em seu *Startup.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="db0ea-122">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```

<span data-ttu-id="db0ea-123">As políticas são aplicadas usando o `Policy` propriedade no `AuthorizeAttribute` atributo:</span><span class="sxs-lookup"><span data-stu-id="db0ea-123">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="db0ea-124">Se você deseja especificar várias funções permitidas em um requisito, você pode especificá-los como parâmetros para o `RequireRole` método:</span><span class="sxs-lookup"><span data-stu-id="db0ea-124">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="db0ea-125">Este exemplo autoriza usuários que pertencem à `Administrator`, `PowerUser` ou `BackupAdministrator` funções.</span><span class="sxs-lookup"><span data-stu-id="db0ea-125">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="db0ea-126">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="db0ea-126">Add Role services to Identity</span></span>

<span data-ttu-id="db0ea-127">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="db0ea-127">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]