---
title: Amostras de autenticação para o ASP.NET Core
author: rick-anderson
description: Fornece links para os exemplos de autenticação no repositório do ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 7b3c911d60ad4737ebd12ce6f7628ad624b11658
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236667"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="f28d4-103">Amostras de autenticação para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f28d4-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="f28d4-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f28d4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f28d4-105">O [repositório do ASP.NET Core](https://github.com/aspnet/AspNetCore) contém os seguintes exemplos de autenticação na *AspNetCore/src/Security/samples* pasta:</span><span class="sxs-lookup"><span data-stu-id="f28d4-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f28d4-106">Transformação de declarações</span><span class="sxs-lookup"><span data-stu-id="f28d4-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="f28d4-107">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="f28d4-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="f28d4-108">Provedor de política personalizada - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f28d4-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f28d4-109">Opções e esquemas de autenticação dinâmico</span><span class="sxs-lookup"><span data-stu-id="f28d4-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="f28d4-110">Declarações externas</span><span class="sxs-lookup"><span data-stu-id="f28d4-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="f28d4-111">Selecionando entre o cookie e o outro esquema de autenticação com base na solicitação</span><span class="sxs-lookup"><span data-stu-id="f28d4-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f28d4-112">Restringe o acesso a arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="f28d4-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f28d4-113">Executar os exemplos</span><span class="sxs-lookup"><span data-stu-id="f28d4-113">Run the samples</span></span>

* <span data-ttu-id="f28d4-114">Selecione uma [ramificação](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f28d4-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="f28d4-115">Por exemplo, `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="f28d4-115">For example, `release/2.2`</span></span>
* <span data-ttu-id="f28d4-116">Clone ou baixe o [repositório do ASP.NET Core](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f28d4-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="f28d4-117">Verifique se você instalou o [SDK do .NET Core](https://www.microsoft.com/net/download/all) versão que corresponde o clone do repositório do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f28d4-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f28d4-118">Navegue até um exemplo na *AspNetCore/src/Security/samples* e executar o exemplo com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f28d4-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>
