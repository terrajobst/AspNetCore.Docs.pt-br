---
title: Versão de compatibilidade do ASP.NET Core MVC
author: rick-anderson
description: Saiba como a classe Startup no ASP.NET Core configura serviços e o pipeline de solicitação do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2018
uid: mvc/compatibility-version
ms.openlocfilehash: 63243d99c7cb74a7e594cd309a808455c6611fc0
ms.sourcegitcommit: 68a3081dd175d6518d1bfa31b4712bd8a2dd3864
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53577845"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="040a4-103">Versão de compatibilidade do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="040a4-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="040a4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="040a4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="040a4-105">O método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite que um aplicativo aceite ou recuse as possíveis alterações da falha de comportamento introduzidas no ASP.NET Core MVC 2.1 ou posteriores.</span><span class="sxs-lookup"><span data-stu-id="040a4-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span> <span data-ttu-id="040a4-106">Essas possíveis alterações da falha de comportamento geralmente afetam como o subsistema MVC se comporta e como **o código do usuário** é chamado pelo tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="040a4-106">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="040a4-107">Ao aceitar, você obtém o comportamento mais recente e o comportamento de longo prazo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="040a4-107">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="040a4-108">O código a seguir define o modo de compatibilidade para o ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="040a4-108">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="040a4-109">É recomendável que você teste seu aplicativo usando a versão mais recente (`CompatibilityVersion.Version_2_2`).</span><span class="sxs-lookup"><span data-stu-id="040a4-109">We recommend you test your app using the latest version (`CompatibilityVersion.Version_2_2`).</span></span> <span data-ttu-id="040a4-110">Estimamos que a maioria dos aplicativos não terão alterações da falha de comportamento usando a versão mais recente.</span><span class="sxs-lookup"><span data-stu-id="040a4-110">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="040a4-111">Os aplicativos que chamam `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` são protegidos contra as possíveis alterações da falha de comportamento apresentadas no ASP.NET Core 2.1 MVC e nas versões 2.x posteriores.</span><span class="sxs-lookup"><span data-stu-id="040a4-111">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1 MVC and later 2.x versions.</span></span> <span data-ttu-id="040a4-112">Essa proteção:</span><span class="sxs-lookup"><span data-stu-id="040a4-112">This protection:</span></span>

* <span data-ttu-id="040a4-113">Não se aplica a todas as alterações da 2.1 e posteriores, ela é direcionada às possíveis alterações da falha de comportamento do tempo de execução do ASP.NET Core no subsistema de MVC.</span><span class="sxs-lookup"><span data-stu-id="040a4-113">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="040a4-114">Não se estende à próxima versão principal.</span><span class="sxs-lookup"><span data-stu-id="040a4-114">Does not extend to the next major version.</span></span>

<span data-ttu-id="040a4-115">A compatibilidade padrão para aplicativos ASP.NET Core 2.1 e 2.x posteriores que **não** é chamada é a `SetCompatibilityVersion` compatibilidade 2.0.</span><span class="sxs-lookup"><span data-stu-id="040a4-115">The default compatibility for ASP.NET Core 2.1 and later 2.x apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="040a4-116">Ou seja, não chamar `SetCompatibilityVersion` é o mesmo que chamar `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="040a4-116">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="040a4-117">O código a seguir define o modo de compatibilidade para o ASP.NET Core 2.2, exceto para os seguintes comportamentos:</span><span class="sxs-lookup"><span data-stu-id="040a4-117">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* [<span data-ttu-id="040a4-118">AllowCombiningAuthorizeFilters</span><span class="sxs-lookup"><span data-stu-id="040a4-118">AllowCombiningAuthorizeFilters</span></span>](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs)
* [<span data-ttu-id="040a4-119">InputFormatterExceptionPolicy</span><span class="sxs-lookup"><span data-stu-id="040a4-119">InputFormatterExceptionPolicy</span></span>](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs)

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="040a4-120">Para aplicativos que encontram alterações da falha de comportamento, o uso das opções de compatibilidade apropriadas:</span><span class="sxs-lookup"><span data-stu-id="040a4-120">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="040a4-121">Permite que você use a versão mais recente e recuse alterações da falha de comportamento específicas.</span><span class="sxs-lookup"><span data-stu-id="040a4-121">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="040a4-122">Tenha tempo para atualizar seu aplicativo para que ele funcione com as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="040a4-122">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="040a4-123">Os comentários do código-fonte da classe [MvcOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs) têm uma boa explicação sobre o que mudou e por que as alterações são uma melhoria para a maioria dos usuários.</span><span class="sxs-lookup"><span data-stu-id="040a4-123">The [MvcOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs) class source comments have a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="040a4-124">Futuramente, haverá uma [versão 3.0 do ASP.NET Core](https://github.com/aspnet/Home/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="040a4-124">At some future date, there will be an [ASP.NET Core 3.0 version](https://github.com/aspnet/Home/wiki/Roadmap).</span></span> <span data-ttu-id="040a4-125">Os comportamentos antigos compatíveis por meio de opções de compatibilidade serão removidos na versão 3.0.</span><span class="sxs-lookup"><span data-stu-id="040a4-125">Old behaviors supported by compatibility switches will be removed in the 3.0 version.</span></span> <span data-ttu-id="040a4-126">Consideramos que essas são alterações positivas que beneficiam quase todos os usuários.</span><span class="sxs-lookup"><span data-stu-id="040a4-126">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="040a4-127">Com a introdução dessas alterações, a maioria dos aplicativos poderá se beneficiar agora e os demais terão tempo para serem atualizados.</span><span class="sxs-lookup"><span data-stu-id="040a4-127">By introducing these changes now, most apps can benefit now, and the others will have time to update their apps.</span></span>
