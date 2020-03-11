---
title: Autorização simples no ASP.NET Core
author: rick-anderson
description: Saiba como usar o atributo Authorize para restringir o acesso a ASP.NET Core de controladores e ações.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663579"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="5f262-103">Autorização simples no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f262-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="5f262-104">A autorização no MVC é controlada por meio do atributo `AuthorizeAttribute` e de seus vários parâmetros.</span><span class="sxs-lookup"><span data-stu-id="5f262-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="5f262-105">Em sua forma mais simples, aplicar o atributo `AuthorizeAttribute` a um controlador ou ação limita o acesso ao controlador ou à ação para qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="5f262-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="5f262-106">Por exemplo, o código a seguir limita o acesso ao `AccountController` a qualquer usuário autenticado.</span><span class="sxs-lookup"><span data-stu-id="5f262-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="5f262-107">Se você quiser aplicar a autorização a uma ação em vez de ao controlador, aplique o atributo `AuthorizeAttribute` à própria ação:</span><span class="sxs-lookup"><span data-stu-id="5f262-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="5f262-108">Agora, somente usuários autenticados podem acessar a função `Logout`.</span><span class="sxs-lookup"><span data-stu-id="5f262-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="5f262-109">Você também pode usar o atributo `AllowAnonymous` para permitir o acesso por usuários não autenticados a ações individuais.</span><span class="sxs-lookup"><span data-stu-id="5f262-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="5f262-110">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5f262-110">For example:</span></span>

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

<span data-ttu-id="5f262-111">Isso permitiria apenas usuários autenticados para o `AccountController`, exceto para a ação de `Login`, que é acessível por todos, independentemente de seu status autenticado ou não autenticado/anônimo.</span><span class="sxs-lookup"><span data-stu-id="5f262-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="5f262-112">`[AllowAnonymous]` ignora todas as instruções de autorização.</span><span class="sxs-lookup"><span data-stu-id="5f262-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="5f262-113">Se você combinar `[AllowAnonymous]` e qualquer atributo de `[Authorize]`, os atributos de `[Authorize]` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="5f262-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="5f262-114">Por exemplo, se você aplicar `[AllowAnonymous]` no nível do controlador, quaisquer atributos `[Authorize]` no mesmo controlador (ou em qualquer ação dentro dele) serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="5f262-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
