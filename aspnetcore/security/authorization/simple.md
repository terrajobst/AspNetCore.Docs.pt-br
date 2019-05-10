---
title: Autorização simples no ASP.NET Core
author: rick-anderson
description: Saiba como usar o atributo Authorize para restringir o acesso a ações e controladores ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897673"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="6191a-103">Autorização simples no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6191a-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="6191a-104">No MVC é controlada por meio de `AuthorizeAttribute` atributo e seus vários parâmetros.</span><span class="sxs-lookup"><span data-stu-id="6191a-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="6191a-105">Em sua forma mais simples, aplicando o `AuthorizeAttribute` de atributo para um acesso de limites de ação ou controlador ao controlador ou ação para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="6191a-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="6191a-106">Por exemplo, o código a seguir limita o acesso para o `AccountController` para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="6191a-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="6191a-107">Se você deseja aplicar a autorização para uma ação em vez do controlador, aplique o `AuthorizeAttribute` de atributo para a ação em si:</span><span class="sxs-lookup"><span data-stu-id="6191a-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="6191a-108">Agora, somente usuários autenticados podem acessar o `Logout` função.</span><span class="sxs-lookup"><span data-stu-id="6191a-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="6191a-109">Você também pode usar o `AllowAnonymous` atributo para permitir o acesso por usuários não autenticados para ações individuais.</span><span class="sxs-lookup"><span data-stu-id="6191a-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="6191a-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="6191a-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="6191a-111">Isso permitiria que somente usuários autenticados para o `AccountController`, exceto para o `Login` ação, que pode ser acessada por todos os usuários, independentemente de seu status de autenticado ou anônimo / não autenticado.</span><span class="sxs-lookup"><span data-stu-id="6191a-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="6191a-112">`[AllowAnonymous]` Ignora todas as instruções de autorização.</span><span class="sxs-lookup"><span data-stu-id="6191a-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="6191a-113">Se você combinar `[AllowAnonymous]` e qualquer `[Authorize]` atributo, o `[Authorize]` atributos são ignorados.</span><span class="sxs-lookup"><span data-stu-id="6191a-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="6191a-114">Por exemplo, se você aplicar `[AllowAnonymous]` no nível do controlador, qualquer `[Authorize]` atributos no mesmo controlador (ou em qualquer ação dentro dele) é ignorada.</span><span class="sxs-lookup"><span data-stu-id="6191a-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
