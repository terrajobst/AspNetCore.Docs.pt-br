---
title: Autorização baseada em modo de exibição no ASP.NET Core MVC
author: rick-anderson
description: Este documento demonstra como injetar e utilizar o serviço de autorização dentro de um modo de exibição do Razor do ASP.NET Core.
ms.author: riande
ms.date: 10/30/2017
uid: security/authorization/views
ms.openlocfilehash: e497c41d4dca29fed8733f18cf727804e3f06d8c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892053"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Autorização baseada em modo de exibição no ASP.NET Core MVC

Um desenvolvedor geralmente deseja mostrar, ocultar ou modificar uma interface do usuário com base na identidade do usuário atual. Você pode acessar o serviço de autorização em modos de exibição do MVC por meio [injeção de dependência](xref:fundamentals/dependency-injection). Para injetar o serviço de autorização em um modo de exibição do Razor, use o `@inject` diretiva:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Se você quiser que o serviço de autorização em cada exibição, coloque o `@inject` diretiva na *viewimports. cshtml* arquivo do *exibições* directory. Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).

Usar o serviço de autorização injetado para invocar `AuthorizeAsync` exatamente da mesma forma que você poderia verificar durante [autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

Em alguns casos, o recurso será o seu modelo de exibição. Invocar `AuthorizeAsync` exatamente da mesma forma que você poderia verificar durante [autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

No código anterior, o modelo é passado como um recurso de que avaliação da política deve levar em consideração.

> [!WARNING]
> Não confie na alternância de visibilidade de elementos de interface do usuário do seu aplicativo como a verificação de autorização única. Ocultar um elemento de interface do usuário pode completamente impede o acesso a sua ação de controlador associado. Por exemplo, considere o botão no trecho de código anterior. Um usuário pode invocar o `Edit` é de URL do método de ação, se ele ou ela sabe que o recurso relativo */Document/Edit/1*. Por esse motivo, o `Edit` método de ação deve realizar sua própria verificação de autorização.
