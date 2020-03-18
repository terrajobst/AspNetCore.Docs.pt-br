---
title: Exemplos de autenticação para ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b013d02a65e752bbb61a87a0bf502785125378a2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511607"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="8018d-103">Exemplos de autenticação para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8018d-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="8018d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8018d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8018d-105">O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="8018d-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="8018d-106">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="8018d-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="8018d-107">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="8018d-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="8018d-108">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="8018d-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="8018d-109">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="8018d-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="8018d-110">Declarações externas</span><span class="sxs-lookup"><span data-stu-id="8018d-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="8018d-111">Seleção entre o cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="8018d-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="8018d-112">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="8018d-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="8018d-113">Executar as amostras</span><span class="sxs-lookup"><span data-stu-id="8018d-113">Run the samples</span></span>

* <span data-ttu-id="8018d-114">Selecione uma [ramificação](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8018d-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="8018d-115">Por exemplo, `Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="8018d-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="8018d-116">Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8018d-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="8018d-117">Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8018d-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="8018d-118">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8018d-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8018d-119">O [repositório de ASP.NET Core](https://github.com/dotnet/AspNetCore) contém os seguintes exemplos de autenticação na pasta *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="8018d-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="8018d-120">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="8018d-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="8018d-121">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="8018d-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="8018d-122">Provedor de política personalizada-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="8018d-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="8018d-123">Opções e esquemas de autenticação dinâmica</span><span class="sxs-lookup"><span data-stu-id="8018d-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="8018d-124">Declarações externas</span><span class="sxs-lookup"><span data-stu-id="8018d-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="8018d-125">Seleção entre o cookie e outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="8018d-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="8018d-126">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="8018d-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="8018d-127">Executar as amostras</span><span class="sxs-lookup"><span data-stu-id="8018d-127">Run the samples</span></span>

* <span data-ttu-id="8018d-128">Selecione uma [ramificação](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8018d-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="8018d-129">Por exemplo, `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="8018d-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="8018d-130">Clone ou baixe o [repositório ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8018d-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="8018d-131">Verifique se você instalou a versão [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) correspondente ao clone do repositório ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8018d-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="8018d-132">Navegue até um exemplo em *AspNetCore/src/Security/Samples* e execute o exemplo com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8018d-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
