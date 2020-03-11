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
# <a name="simple-authorization-in-aspnet-core"></a>Autorização simples no ASP.NET Core

<a name="security-authorization-simple"></a>

A autorização no MVC é controlada por meio do atributo `AuthorizeAttribute` e de seus vários parâmetros. Em sua forma mais simples, aplicar o atributo `AuthorizeAttribute` a um controlador ou ação limita o acesso ao controlador ou à ação para qualquer usuário autenticado.

Por exemplo, o código a seguir limita o acesso ao `AccountController` a qualquer usuário autenticado.

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

Se você quiser aplicar a autorização a uma ação em vez de ao controlador, aplique o atributo `AuthorizeAttribute` à própria ação:

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

Agora, somente usuários autenticados podem acessar a função `Logout`.

Você também pode usar o atributo `AllowAnonymous` para permitir o acesso por usuários não autenticados a ações individuais. Por exemplo:

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

Isso permitiria apenas usuários autenticados para o `AccountController`, exceto para a ação de `Login`, que é acessível por todos, independentemente de seu status autenticado ou não autenticado/anônimo.

> [!WARNING]
> `[AllowAnonymous]` ignora todas as instruções de autorização. Se você combinar `[AllowAnonymous]` e qualquer atributo de `[Authorize]`, os atributos de `[Authorize]` serão ignorados. Por exemplo, se você aplicar `[AllowAnonymous]` no nível do controlador, quaisquer atributos `[Authorize]` no mesmo controlador (ou em qualquer ação dentro dele) serão ignorados.
