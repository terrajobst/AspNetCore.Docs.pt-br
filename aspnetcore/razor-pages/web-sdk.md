---
title: SDK do ASP.NET Core Web
author: Rick-Anderson
description: Visão geral de Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869760"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="70087-103">SDK do ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="70087-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="70087-104">{1&gt;Visão Geral&lt;1}</span><span class="sxs-lookup"><span data-stu-id="70087-104">Overview</span></span>

<span data-ttu-id="70087-105">`Microsoft.NET.Sdk.Web` é um [SDK de projeto do MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para compilar ASP.NET Core aplicativos.</span><span class="sxs-lookup"><span data-stu-id="70087-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="70087-106">É possível criar um aplicativo ASP.NET Core sem esse SDK, no entanto, o SDK da Web é:</span><span class="sxs-lookup"><span data-stu-id="70087-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="70087-107">Adaptado em direção à oferta de uma experiência de primeira classe.</span><span class="sxs-lookup"><span data-stu-id="70087-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="70087-108">O destino recomendado para a maioria dos usuários.</span><span class="sxs-lookup"><span data-stu-id="70087-108">The recommended target for most users.</span></span>

<span data-ttu-id="70087-109">Use o Web. SDK em um projeto do:</span><span class="sxs-lookup"><span data-stu-id="70087-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="70087-110">Recursos habilitados usando o SDK da Web:</span><span class="sxs-lookup"><span data-stu-id="70087-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="70087-111">Projetos voltados para o .NET Core 3,0 ou posterior referência implícita:</span><span class="sxs-lookup"><span data-stu-id="70087-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="70087-112">A [estrutura compartilhada ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="70087-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="70087-113">[Analisadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) projetados para a criação de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70087-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="70087-114">O SDK da Web importa destinos do MSBuild que habilitam o uso de perfis de publicação e publicação usando o WebDeploy.</span><span class="sxs-lookup"><span data-stu-id="70087-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="70087-115">{1&gt;Propriedades&lt;1}</span><span class="sxs-lookup"><span data-stu-id="70087-115">Properties</span></span>

| <span data-ttu-id="70087-116">propriedade</span><span class="sxs-lookup"><span data-stu-id="70087-116">Property</span></span> | <span data-ttu-id="70087-117">Descrição</span><span class="sxs-lookup"><span data-stu-id="70087-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="70087-118">Desabilita a referência implícita para a estrutura compartilhada `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="70087-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="70087-119">Desabilita a referência implícita a ASP.NET Core analisadores.</span><span class="sxs-lookup"><span data-stu-id="70087-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="70087-120">Desabilita a referência implícita aos analisadores de componentes do Razor ao compilar aplicativos de Blazor (servidor).</span><span class="sxs-lookup"><span data-stu-id="70087-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
