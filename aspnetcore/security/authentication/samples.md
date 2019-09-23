---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: d49aef198e926d88f1a6727f84b06f0861c8812d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187290"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="6630a-103">Exemplos de autenticação para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6630a-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="6630a-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6630a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6630a-105">O [repositório de ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="6630a-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="6630a-106">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="6630a-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="6630a-107">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="6630a-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="6630a-108">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="6630a-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="6630a-109">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="6630a-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="6630a-110">Declarações externas</span><span class="sxs-lookup"><span data-stu-id="6630a-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="6630a-111">Seleção entre o cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="6630a-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="6630a-112">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="6630a-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="6630a-113">Executar os exemplos</span><span class="sxs-lookup"><span data-stu-id="6630a-113">Run the samples</span></span>

* <span data-ttu-id="6630a-114">Selecione uma [ramificação](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6630a-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="6630a-115">Por exemplo, `Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="6630a-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="6630a-116">Clone ou baixe o [repositório ASP.NET Core](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6630a-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="6630a-117">Verifique se você instalou a versão [SDK do .NET Core](https://www.microsoft.com/net/download/all) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6630a-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="6630a-118">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.</span><span class="sxs-lookup"><span data-stu-id="6630a-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6630a-119">O [repositório de ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="6630a-119">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="6630a-120">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="6630a-120">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="6630a-121">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="6630a-121">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="6630a-122">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="6630a-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="6630a-123">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="6630a-123">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="6630a-124">Declarações externas</span><span class="sxs-lookup"><span data-stu-id="6630a-124">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="6630a-125">Seleção entre o cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="6630a-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="6630a-126">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="6630a-126">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="6630a-127">Executar os exemplos</span><span class="sxs-lookup"><span data-stu-id="6630a-127">Run the samples</span></span>

* <span data-ttu-id="6630a-128">Selecione uma [ramificação](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6630a-128">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="6630a-129">Por exemplo, `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="6630a-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="6630a-130">Clone ou baixe o [repositório ASP.NET Core](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="6630a-130">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="6630a-131">Verifique se você instalou a versão [SDK do .NET Core](https://www.microsoft.com/net/download/all) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6630a-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="6630a-132">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.</span><span class="sxs-lookup"><span data-stu-id="6630a-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
