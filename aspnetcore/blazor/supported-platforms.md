---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para Blazorde ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160126"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a><span data-ttu-id="077fd-103">ASP.NET Core Blazor plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="077fd-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="077fd-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="077fd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="077fd-105">Requisitos de navegador</span><span class="sxs-lookup"><span data-stu-id="077fd-105">Browser requirements</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="077fd-106"> Webassembly</span><span class="sxs-lookup"><span data-stu-id="077fd-106"> WebAssembly</span></span>

| <span data-ttu-id="077fd-107">Navegador</span><span class="sxs-lookup"><span data-stu-id="077fd-107">Browser</span></span>                          | <span data-ttu-id="077fd-108">Versão do</span><span class="sxs-lookup"><span data-stu-id="077fd-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="077fd-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="077fd-109">Microsoft Edge</span></span>                   | <span data-ttu-id="077fd-110">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-110">Current</span></span>               |
| <span data-ttu-id="077fd-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="077fd-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="077fd-112">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-112">Current</span></span>               |
| <span data-ttu-id="077fd-113">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="077fd-113">Google Chrome, including Android</span></span> | <span data-ttu-id="077fd-114">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-114">Current</span></span>               |
| <span data-ttu-id="077fd-115">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="077fd-115">Safari, including iOS</span></span>            | <span data-ttu-id="077fd-116">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-116">Current</span></span>               |
| <span data-ttu-id="077fd-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="077fd-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="077fd-118">Sem suporte&dagger;</span><span class="sxs-lookup"><span data-stu-id="077fd-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="077fd-119">O &dagger;Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="077fd-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="opno-locblazor-server"></a><span data-ttu-id="077fd-120">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="077fd-120">Blazor Server</span></span>

| <span data-ttu-id="077fd-121">Navegador</span><span class="sxs-lookup"><span data-stu-id="077fd-121">Browser</span></span>                          | <span data-ttu-id="077fd-122">Versão do</span><span class="sxs-lookup"><span data-stu-id="077fd-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="077fd-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="077fd-123">Microsoft Edge</span></span>                   | <span data-ttu-id="077fd-124">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-124">Current</span></span>    |
| <span data-ttu-id="077fd-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="077fd-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="077fd-126">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-126">Current</span></span>    |
| <span data-ttu-id="077fd-127">Google Chrome, incluindo Android</span><span class="sxs-lookup"><span data-stu-id="077fd-127">Google Chrome, including Android</span></span> | <span data-ttu-id="077fd-128">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-128">Current</span></span>    |
| <span data-ttu-id="077fd-129">Safari, incluindo iOS</span><span class="sxs-lookup"><span data-stu-id="077fd-129">Safari, including iOS</span></span>            | <span data-ttu-id="077fd-130">Atual</span><span class="sxs-lookup"><span data-stu-id="077fd-130">Current</span></span>    |
| <span data-ttu-id="077fd-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="077fd-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="077fd-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="077fd-132">11&dagger;</span></span> |

<span data-ttu-id="077fd-133">&dagger;outros suportes retroativos são necessários (por exemplo, as promessas podem ser adicionadas por meio de um pacote [polyfill.Io](https://polyfill.io/v3/) ).</span><span class="sxs-lookup"><span data-stu-id="077fd-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="077fd-134">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="077fd-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
