---
title: Autorização baseada em função no ASP.NET Core
author: rick-anderson
description: Saiba como restringir o acesso ao controlador de ASP.NET Core e à ação passando funções para o atributo Authorize.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 28aa3df6aa661d0b762df78fe611cd827af43f75
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73660047"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="23e24-103">Autorização baseada em função no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23e24-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="23e24-104">Quando uma identidade é criada, ela pode pertencer a uma ou mais funções.</span><span class="sxs-lookup"><span data-stu-id="23e24-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="23e24-105">Por exemplo, Tracy pode pertencer ao administrador e às funções de usuário, enquanto Scott só pode pertencer à função de usuário.</span><span class="sxs-lookup"><span data-stu-id="23e24-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="23e24-106">A forma como essas funções são criadas e gerenciadas depende do armazenamento de backup do processo de autorização.</span><span class="sxs-lookup"><span data-stu-id="23e24-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="23e24-107">As funções são expostas ao desenvolvedor por meio do método [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) na classe [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .</span><span class="sxs-lookup"><span data-stu-id="23e24-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="23e24-108">Adicionando verificações de função</span><span class="sxs-lookup"><span data-stu-id="23e24-108">Adding role checks</span></span>

<span data-ttu-id="23e24-109">As verificações de autorização baseadas em função são declarativas&mdash;o desenvolvedor as incorpora em seu código, em um controlador ou em uma ação dentro de um controlador, especificando funções das quais o usuário atual deve ser membro para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="23e24-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="23e24-110">Por exemplo, o código a seguir limita o acesso a qualquer ação no `AdministrationController` aos usuários que são membros da função `Administrator`:</span><span class="sxs-lookup"><span data-stu-id="23e24-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="23e24-111">Você pode especificar várias funções como uma lista separada por vírgulas:</span><span class="sxs-lookup"><span data-stu-id="23e24-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="23e24-112">Esse controlador só pode ser acessado por usuários que são membros da função `HRManager` ou da função `Finance`.</span><span class="sxs-lookup"><span data-stu-id="23e24-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="23e24-113">Se você aplicar vários atributos, um usuário de acesso deverá ser um membro de todas as funções especificadas; o exemplo a seguir requer que um usuário seja membro da função `PowerUser` e `ControlPanelUser`.</span><span class="sxs-lookup"><span data-stu-id="23e24-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="23e24-114">Você pode limitar ainda mais o acesso aplicando atributos de autorização de função adicionais no nível de ação:</span><span class="sxs-lookup"><span data-stu-id="23e24-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

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

<span data-ttu-id="23e24-115">No trecho de código anterior, os membros da função `Administrator` ou a função `PowerUser` podem acessar o controlador e a ação `SetTime`, mas somente os membros da função `Administrator` podem acessar a ação `ShutDown`.</span><span class="sxs-lookup"><span data-stu-id="23e24-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="23e24-116">Você também pode bloquear um controlador, mas permitir acesso anônimo e não autenticado a ações individuais.</span><span class="sxs-lookup"><span data-stu-id="23e24-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="23e24-117">Por Razor Pages, o `AuthorizeAttribute` pode ser aplicado por um dos dois:</span><span class="sxs-lookup"><span data-stu-id="23e24-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="23e24-118">Usando uma [Convenção](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)ou</span><span class="sxs-lookup"><span data-stu-id="23e24-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="23e24-119">Aplicando o `AuthorizeAttribute` à instância de `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="23e24-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="23e24-120">Os atributos de filtro, incluindo `AuthorizeAttribute`, só podem ser aplicados a PageModel e não podem ser aplicados a métodos de manipuladores de páginas específicos.</span><span class="sxs-lookup"><span data-stu-id="23e24-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="23e24-121">Verificações de função baseadas em política</span><span class="sxs-lookup"><span data-stu-id="23e24-121">Policy based role checks</span></span>

<span data-ttu-id="23e24-122">Os requisitos de função também podem ser expressos usando a nova sintaxe de política, em que um desenvolvedor registra uma política na inicialização como parte da configuração do serviço de autorização.</span><span class="sxs-lookup"><span data-stu-id="23e24-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="23e24-123">Isso normalmente ocorre em `ConfigureServices()` no arquivo *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="23e24-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
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
::: moniker-end

<span data-ttu-id="23e24-124">As políticas são aplicadas usando a propriedade `Policy` no atributo `AuthorizeAttribute`:</span><span class="sxs-lookup"><span data-stu-id="23e24-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="23e24-125">Se você quiser especificar várias funções permitidas em um requisito, poderá especificá-las como parâmetros para o método de `RequireRole`:</span><span class="sxs-lookup"><span data-stu-id="23e24-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="23e24-126">Este exemplo autoriza os usuários que pertencem às funções `Administrator`, `PowerUser` ou `BackupAdministrator`.</span><span class="sxs-lookup"><span data-stu-id="23e24-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="23e24-127">Adicionar serviços de função à identidade</span><span class="sxs-lookup"><span data-stu-id="23e24-127">Add Role services to Identity</span></span>

<span data-ttu-id="23e24-128">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="23e24-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

