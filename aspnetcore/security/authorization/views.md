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
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="c3b8b-103">Autorização baseada em modo de exibição no ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c3b8b-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="c3b8b-104">Um desenvolvedor geralmente deseja mostrar, ocultar ou modificar uma interface do usuário com base na identidade do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="c3b8b-105">Você pode acessar o serviço de autorização em modos de exibição do MVC por meio [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c3b8b-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c3b8b-106">Para injetar o serviço de autorização em um modo de exibição do Razor, use o `@inject` diretiva:</span><span class="sxs-lookup"><span data-stu-id="c3b8b-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="c3b8b-107">Se você quiser que o serviço de autorização em cada exibição, coloque o `@inject` diretiva na *viewimports. cshtml* arquivo do *exibições* directory.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="c3b8b-108">Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c3b8b-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="c3b8b-109">Usar o serviço de autorização injetado para invocar `AuthorizeAsync` exatamente da mesma forma que você poderia verificar durante [autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="c3b8b-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c3b8b-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c3b8b-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c3b8b-111">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c3b8b-111">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

<span data-ttu-id="c3b8b-112">Em alguns casos, o recurso será o seu modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-112">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="c3b8b-113">Invocar `AuthorizeAsync` exatamente da mesma forma que você poderia verificar durante [autorização baseada em recursos](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="c3b8b-113">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c3b8b-114">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c3b8b-114">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c3b8b-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c3b8b-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

<span data-ttu-id="c3b8b-116">No código anterior, o modelo é passado como um recurso de que avaliação da política deve levar em consideração.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-116">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="c3b8b-117">Não confie na alternância de visibilidade de elementos de interface do usuário do seu aplicativo como a verificação de autorização única.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-117">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="c3b8b-118">Ocultar um elemento de interface do usuário pode completamente impede o acesso a sua ação de controlador associado.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-118">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="c3b8b-119">Por exemplo, considere o botão no trecho de código anterior.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-119">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="c3b8b-120">Um usuário pode invocar o `Edit` é de URL do método de ação, se ele ou ela sabe que o recurso relativo */Document/Edit/1*.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-120">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="c3b8b-121">Por esse motivo, o `Edit` método de ação deve realizar sua própria verificação de autorização.</span><span class="sxs-lookup"><span data-stu-id="c3b8b-121">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
