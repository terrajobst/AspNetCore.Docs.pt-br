---
title: Esquemas de política no ASP.NET Core
author: rick-anderson
description: Esquemas de política de autenticação torná-lo mais fácil ter um esquema de autenticação lógica
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: 1a2d92e6fa54189b8154fc501b31c8a99d1f9081
ms.sourcegitcommit: 357a7120632b20465801c093e4e5bd4a315496a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649173"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="e9a3f-103">Esquemas de política no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9a3f-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="e9a3f-104">Esquemas de política de autenticação torná-lo mais fácil ter um esquema de autenticação lógica potencialmente usar várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="e9a3f-105">Por exemplo, um esquema de política pode usar autenticação do Google para desafios e autenticação de cookie para todo o resto.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="e9a3f-106">Esquemas de política de autenticação torná-lo:</span><span class="sxs-lookup"><span data-stu-id="e9a3f-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="e9a3f-107">Fácil encaminhar qualquer ação de autenticação para outro esquema.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="e9a3f-108">Encaminhar dinamicamente com base na solicitação.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="e9a3f-109">Todos os esquemas de autenticação que use derivado <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> e os respectivos [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="e9a3f-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [`AuthenticationHandler<TOptions>`](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="e9a3f-110">São automaticamente esquemas de política no ASP.NET Core 2.1 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="e9a3f-111">Pode ser habilitado por meio de configuração de opções do esquema.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="e9a3f-112">Exemplos</span><span class="sxs-lookup"><span data-stu-id="e9a3f-112">Examples</span></span>

<span data-ttu-id="e9a3f-113">O exemplo a seguir mostra um esquema de nível superior que combina os esquemas de nível inferiores.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="e9a3f-114">Autenticação do Google é usada para os desafios e autenticação de cookie é usada para todo o resto:</span><span class="sxs-lookup"><span data-stu-id="e9a3f-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="e9a3f-115">O exemplo a seguir habilita a seleção dinâmica de esquemas em uma base por solicitação.</span><span class="sxs-lookup"><span data-stu-id="e9a3f-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="e9a3f-116">Ou seja, como a combinação de cookies e autenticação de API:</span><span class="sxs-lookup"><span data-stu-id="e9a3f-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
