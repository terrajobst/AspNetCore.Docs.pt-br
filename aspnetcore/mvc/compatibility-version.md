---
title: Versão de compatibilidade do ASP.NET Core MVC
author: rick-anderson
description: Saiba como a classe Startup no ASP.NET Core configura serviços e o pipeline de solicitação do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b29e2ee49aaf0f557f1acd0cf03e9e82d5ea0105
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667121"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="3e72a-103">Versão de compatibilidade do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3e72a-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="3e72a-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3e72a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e72a-105">O método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> é um não operacional para aplicativos ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="3e72a-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="3e72a-106">Ou seja, chamar `SetCompatibilityVersion` com qualquer valor de <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> não tem impacto sobre o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e72a-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="3e72a-107">A próxima versão secundária do ASP.NET Core pode fornecer um novo valor de `CompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="3e72a-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="3e72a-108">`CompatibilityVersion` valores `Version_2_0` por meio de `Version_2_2` são marcados `[Obsolete(...)]`.</span><span class="sxs-lookup"><span data-stu-id="3e72a-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="3e72a-109">Veja [alterações de API de interrupção em antifalsificação, CORS, diagnóstico, MVC e roteamento](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="3e72a-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="3e72a-110">Essa lista inclui alterações significativas para as opções de compatibilidade.</span><span class="sxs-lookup"><span data-stu-id="3e72a-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="3e72a-111">Para ver como `SetCompatibilityVersion` funciona com os aplicativos ASP.NET Core 2. x, selecione a [versão ASP.NET Core 2,2 deste artigo](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="3e72a-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e72a-112">O método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite que um aplicativo ASP.NET Core 2. x aceite ou recuse alterações de comportamento potencialmente quebradas introduzidas no ASP.NET Core MVC 2,1 ou 2,2.</span><span class="sxs-lookup"><span data-stu-id="3e72a-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="3e72a-113">Essas possíveis alterações da falha de comportamento geralmente afetam como o subsistema MVC se comporta e como **o código do usuário** é chamado pelo runtime.</span><span class="sxs-lookup"><span data-stu-id="3e72a-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="3e72a-114">Ao aceitar, você obtém o comportamento mais recente e o comportamento de longo prazo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e72a-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="3e72a-115">O código a seguir define o modo de compatibilidade para o ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="3e72a-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="3e72a-116">É recomendável que você teste seu aplicativo usando a versão mais recente (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="3e72a-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="3e72a-117">Estimamos que a maioria dos aplicativos não terão alterações da falha de comportamento usando a versão mais recente.</span><span class="sxs-lookup"><span data-stu-id="3e72a-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="3e72a-118">Os aplicativos que chamam `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` são protegidos contra alterações de comportamento potencialmente quebras introduzidas nas versões MVC do ASP.NET Core 2.1/2.2.</span><span class="sxs-lookup"><span data-stu-id="3e72a-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="3e72a-119">Essa proteção:</span><span class="sxs-lookup"><span data-stu-id="3e72a-119">This protection:</span></span>

* <span data-ttu-id="3e72a-120">Não se aplica a todas as alterações da 2.1 e posteriores, ela é direcionada às possíveis alterações da falha de comportamento do runtime do ASP.NET Core no subsistema de MVC.</span><span class="sxs-lookup"><span data-stu-id="3e72a-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="3e72a-121">Não estende para o ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="3e72a-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="3e72a-122">A compatibilidade padrão para os aplicativos ASP.NET Core 2,1 e 2,2 que **não** chamam `SetCompatibilityVersion` é a compatibilidade 2,0.</span><span class="sxs-lookup"><span data-stu-id="3e72a-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="3e72a-123">Ou seja, não chamar `SetCompatibilityVersion` é o mesmo que chamar `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="3e72a-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="3e72a-124">O código a seguir define o modo de compatibilidade para o ASP.NET Core 2.2, exceto para os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="3e72a-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="3e72a-125">Para aplicativos que encontram alterações da falha de comportamento, o uso das opções de compatibilidade apropriadas:</span><span class="sxs-lookup"><span data-stu-id="3e72a-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="3e72a-126">Permite que você use a versão mais recente e recuse alterações da falha de comportamento específicas.</span><span class="sxs-lookup"><span data-stu-id="3e72a-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="3e72a-127">Tenha tempo para atualizar seu aplicativo para que ele funcione com as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="3e72a-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="3e72a-128">A documentação <xref:Microsoft.AspNetCore.Mvc.MvcOptions> tem uma boa explicação sobre o que mudou e por que as alterações são uma melhoria para a maioria dos usuários.</span><span class="sxs-lookup"><span data-stu-id="3e72a-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="3e72a-129">Com o ASP.NET Core 3,0, os comportamentos antigos com suporte das opções de compatibilidade foram removidos.</span><span class="sxs-lookup"><span data-stu-id="3e72a-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="3e72a-130">Consideramos que essas são alterações positivas que beneficiam quase todos os usuários.</span><span class="sxs-lookup"><span data-stu-id="3e72a-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="3e72a-131">Ao introduzir essas alterações em 2,1 e 2,2, a maioria dos aplicativos pode se beneficiar, enquanto outras têm tempo para atualizar.</span><span class="sxs-lookup"><span data-stu-id="3e72a-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end
