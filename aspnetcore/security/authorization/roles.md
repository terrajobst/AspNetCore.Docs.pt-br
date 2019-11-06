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
# <a name="role-based-authorization-in-aspnet-core"></a>Autorização baseada em função no ASP.NET Core

<a name="security-authorization-role-based"></a>

Quando uma identidade é criada, ela pode pertencer a uma ou mais funções. Por exemplo, Tracy pode pertencer ao administrador e às funções de usuário, enquanto Scott só pode pertencer à função de usuário. A forma como essas funções são criadas e gerenciadas depende do armazenamento de backup do processo de autorização. As funções são expostas ao desenvolvedor por meio do método [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) na classe [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .

## <a name="adding-role-checks"></a>Adicionando verificações de função

As verificações de autorização baseadas em função são declarativas&mdash;o desenvolvedor as incorpora em seu código, em um controlador ou em uma ação dentro de um controlador, especificando funções das quais o usuário atual deve ser membro para acessar o recurso solicitado.

Por exemplo, o código a seguir limita o acesso a qualquer ação no `AdministrationController` aos usuários que são membros da função `Administrator`:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Você pode especificar várias funções como uma lista separada por vírgulas:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Esse controlador só pode ser acessado por usuários que são membros da função `HRManager` ou da função `Finance`.

Se você aplicar vários atributos, um usuário de acesso deverá ser um membro de todas as funções especificadas; o exemplo a seguir requer que um usuário seja membro da função `PowerUser` e `ControlPanelUser`.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Você pode limitar ainda mais o acesso aplicando atributos de autorização de função adicionais no nível de ação:

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

No trecho de código anterior, os membros da função `Administrator` ou a função `PowerUser` podem acessar o controlador e a ação `SetTime`, mas somente os membros da função `Administrator` podem acessar a ação `ShutDown`.

Você também pode bloquear um controlador, mas permitir acesso anônimo e não autenticado a ações individuais.

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

Por Razor Pages, o `AuthorizeAttribute` pode ser aplicado por um dos dois:

* Usando uma [Convenção](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)ou
* Aplicando o `AuthorizeAttribute` à instância de `PageModel`:

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
> Os atributos de filtro, incluindo `AuthorizeAttribute`, só podem ser aplicados a PageModel e não podem ser aplicados a métodos de manipuladores de páginas específicos.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Verificações de função baseadas em política

Os requisitos de função também podem ser expressos usando a nova sintaxe de política, em que um desenvolvedor registra uma política na inicialização como parte da configuração do serviço de autorização. Isso normalmente ocorre em `ConfigureServices()` no arquivo *Startup.cs* .

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

As políticas são aplicadas usando a propriedade `Policy` no atributo `AuthorizeAttribute`:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Se você quiser especificar várias funções permitidas em um requisito, poderá especificá-las como parâmetros para o método de `RequireRole`:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Este exemplo autoriza os usuários que pertencem às funções `Administrator`, `PowerUser` ou `BackupAdministrator`.

### <a name="add-role-services-to-identity"></a>Adicionar serviços de função à identidade

Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

