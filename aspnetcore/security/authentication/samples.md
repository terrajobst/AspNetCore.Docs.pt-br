---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: efa177245faceddad4eb80de9e6f6d38e1a4261c
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022421"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="2b2b3-103">Exemplos de autenticação para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b2b3-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="2b2b3-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2b2b3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2b2b3-105">O [repositório de ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="2b2b3-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="2b2b3-106">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="2b2b3-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="2b2b3-107">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="2b2b3-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="2b2b3-108">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="2b2b3-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="2b2b3-109">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="2b2b3-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="2b2b3-110">Declarações externas</span><span class="sxs-lookup"><span data-stu-id="2b2b3-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="2b2b3-111">Seleção entre o cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="2b2b3-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="2b2b3-112">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2b2b3-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="2b2b3-113">Executar os exemplos</span><span class="sxs-lookup"><span data-stu-id="2b2b3-113">Run the samples</span></span>

* <span data-ttu-id="2b2b3-114">Selecione uma [ramificação](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2b2b3-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="2b2b3-115">Por exemplo, `Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="2b2b3-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="2b2b3-116">Clone ou baixe o [repositório ASP.NET Core](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2b2b3-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="2b2b3-117">Verifique se você instalou a versão [SDK do .NET Core](https://www.microsoft.com/net/download/all) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2b2b3-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="2b2b3-118">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.</span><span class="sxs-lookup"><span data-stu-id="2b2b3-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2b2b3-119">O [repositório de ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="2b2b3-119">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="2b2b3-120">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="2b2b3-120">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="2b2b3-121">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="2b2b3-121">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="2b2b3-122">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="2b2b3-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="2b2b3-123">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="2b2b3-123">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="2b2b3-124">Declarações externas</span><span class="sxs-lookup"><span data-stu-id="2b2b3-124">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="2b2b3-125">Seleção entre o cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="2b2b3-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="2b2b3-126">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2b2b3-126">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="2b2b3-127">Executar os exemplos</span><span class="sxs-lookup"><span data-stu-id="2b2b3-127">Run the samples</span></span>

* <span data-ttu-id="2b2b3-128">Selecione uma [ramificação](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2b2b3-128">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="2b2b3-129">Por exemplo, `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="2b2b3-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="2b2b3-130">Clone ou baixe o [repositório ASP.NET Core](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2b2b3-130">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="2b2b3-131">Verifique se você instalou a versão [SDK do .NET Core](https://www.microsoft.com/net/download/all) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2b2b3-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="2b2b3-132">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo `dotnet run`com.</span><span class="sxs-lookup"><span data-stu-id="2b2b3-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
