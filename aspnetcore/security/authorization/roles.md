---
title: Autorização baseada em função no ASP.NET Core
author: rick-anderson
description: Aprenda a restringir o acesso de controlador e ação do ASP.NET Core, passando as funções para o atributo Authorize.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 0e01e1976e2721ca64720a67c6341661f646395c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894113"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Autorização baseada em função no ASP.NET Core

<a name="security-authorization-role-based"></a>

Quando uma identidade é criada ele pode pertencer a uma ou mais funções. Por exemplo, Tracy pode pertencer às funções de administrador e usuário, embora Scott só pode pertencer à função de usuário. Como essas funções são criadas e gerenciadas dependem do repositório de backup do processo de autorização. As funções são expostas para o desenvolvedor por meio de [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) método na [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) classe.

## <a name="adding-role-checks"></a>Adicionar verificações de função

Verificações de autorização baseado em função são declarativas&mdash;o desenvolvedor incorpora-los dentro de seu código, em relação a um controlador ou uma ação dentro de um controlador, especificando as funções que o usuário atual deve ser um membro de acessar o recurso solicitado.

Por exemplo, o código a seguir limita o acesso a quaisquer ações sobre o `AdministrationController` aos usuários que são membros do `Administrator` função:

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

Esse controlador seria apenas ser acessado por usuários que são membros do `HRManager` função ou o `Finance` função.

Se você aplicar vários atributos de um usuário ao acessar deve ser um membro de todas as funções especificadas; o exemplo a seguir requer que um usuário deve ser um membro de ambos os `PowerUser` e `ControlPanelUser` função.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Você pode limitar o acesso ainda mais aplicando atributos de autorização de função adicionais em nível de ação:

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

Nos membros de trecho de código anterior do `Administrator` função ou o `PowerUser` função pode acessar o controlador e o `SetTime` ação, mas somente os membros dos `Administrator` função pode acessar o `ShutDown` ação.

Você também pode bloquear um controlador mas permitir o acesso anônimo, não autenticado a ações individuais.

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

Para as páginas Razor, o `AuthorizeAttribute` podem ser aplicadas por qualquer um:

* Usando um [convenção](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), ou
* Aplicando o `AuthorizeAttribute` para o `PageModel` instância:

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
> Atributos de filtro, incluindo `AuthorizeAttribute`, só pode ser aplicado a PageModel e não pode ser aplicado a métodos do manipulador de página específica.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Verificações de função baseada em política

Requisitos da função também podem ser expressos usando a nova sintaxe de diretiva, em que um desenvolvedor registra uma política na inicialização como parte da configuração do serviço de autorização. Isso normalmente ocorre `ConfigureServices()` em seu *Startup.cs* arquivo.

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

As políticas são aplicadas usando o `Policy` propriedade no `AuthorizeAttribute` atributo:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Se você deseja especificar várias funções permitidas em um requisito, você pode especificá-los como parâmetros para o `RequireRole` método:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Este exemplo autoriza usuários que pertencem à `Administrator`, `PowerUser` ou `BackupAdministrator` funções.

### <a name="add-role-services-to-identity"></a>Adicionar serviços de função à identidade

Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]
