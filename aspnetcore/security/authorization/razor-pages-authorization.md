---
title: Convenções de autorização de páginas do Razor no ASP.NET Core
author: guardrex
description: Saiba como controlar o acesso a páginas com as convenções de autorizam usuários e permitir que usuários anônimos acessem páginas ou pastas de páginas.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ff061f96f30cd893b903403de760a172c924cf06
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895413"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="c1576-103">Convenções de autorização de páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1576-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="c1576-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1576-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c1576-105">Uma maneira de controlar o acesso em seu aplicativo de páginas do Razor é usar as convenções de autorização na inicialização.</span><span class="sxs-lookup"><span data-stu-id="c1576-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="c1576-106">Essas convenções permitem que você autorizar usuários e permitir que usuários anônimos acessem páginas individuais ou pastas de páginas.</span><span class="sxs-lookup"><span data-stu-id="c1576-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="c1576-107">Aplicam as convenções descritas neste tópico automaticamente [filtros de autorização](xref:mvc/controllers/filters#authorization-filters) para controlar o acesso.</span><span class="sxs-lookup"><span data-stu-id="c1576-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="c1576-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1576-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c1576-109">O aplicativo de exemplo usa [autenticação de cookie sem o ASP.NET Core Identity](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="c1576-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="c1576-110">Os conceitos e os exemplos mostrados neste tópico se aplicam igualmente a aplicativos que usam o ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="c1576-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="c1576-111">Para usar a identidade do ASP.NET Core, siga as orientações em <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="c1576-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="c1576-112">Exigir autorização para acessar uma página</span><span class="sxs-lookup"><span data-stu-id="c1576-112">Require authorization to access a page</span></span>

<span data-ttu-id="c1576-113">Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para a página no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="c1576-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="c1576-114">O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor sem uma extensão e que contém apenas barras "/".</span><span class="sxs-lookup"><span data-stu-id="c1576-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="c1576-115">Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizePage sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="c1576-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="c1576-116">Uma <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> pode ser aplicado a uma classe de modelo de página com o `[Authorize]` atributo de filtro.</span><span class="sxs-lookup"><span data-stu-id="c1576-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="c1576-117">Para obter mais informações, consulte [atributo de filtro Authorize](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="c1576-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="c1576-118">Exigir autorização para acessar uma pasta de páginas</span><span class="sxs-lookup"><span data-stu-id="c1576-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="c1576-119">Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para todas as páginas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="c1576-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="c1576-120">O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="c1576-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="c1576-121">Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizeFolder sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="c1576-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="c1576-122">Exigir autorização para acessar uma página de área</span><span class="sxs-lookup"><span data-stu-id="c1576-122">Require authorization to access an area page</span></span>

<span data-ttu-id="c1576-123">Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para a página de área no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="c1576-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="c1576-124">O nome da página é o caminho do arquivo sem uma extensão relativo ao diretório raiz páginas para a área especificada.</span><span class="sxs-lookup"><span data-stu-id="c1576-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c1576-125">Por exemplo, o nome da página para o arquivo *Areas/Identity/Pages/Manage/Accounts.cshtml* é *contas/gerenciar/*.</span><span class="sxs-lookup"><span data-stu-id="c1576-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="c1576-126">Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizeAreaPage sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="c1576-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="c1576-127">Exigir autorização para acessar uma pasta de áreas</span><span class="sxs-lookup"><span data-stu-id="c1576-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="c1576-128">Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> para todas as áreas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="c1576-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="c1576-129">O caminho da pasta é o caminho da pasta, relativo ao diretório raiz páginas para a área especificada.</span><span class="sxs-lookup"><span data-stu-id="c1576-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c1576-130">Por exemplo, o caminho da pasta para os arquivos sob *áreas/identidade/páginas/gerenciar/* é */gerenciar*.</span><span class="sxs-lookup"><span data-stu-id="c1576-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="c1576-131">Para especificar uma [política de autorização](xref:security/authorization/policies), use um [AuthorizeAreaFolder sobrecarga](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="c1576-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="c1576-132">Permitir o acesso anônimo para uma página</span><span class="sxs-lookup"><span data-stu-id="c1576-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="c1576-133">Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> para uma página no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="c1576-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="c1576-134">O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor sem uma extensão e que contém apenas barras "/".</span><span class="sxs-lookup"><span data-stu-id="c1576-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="c1576-135">Permitir o acesso anônimo para uma pasta de páginas</span><span class="sxs-lookup"><span data-stu-id="c1576-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="c1576-136">Use o <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convenção via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para adicionar um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> para todas as páginas em uma pasta no caminho especificado:</span><span class="sxs-lookup"><span data-stu-id="c1576-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="c1576-137">O caminho especificado é o caminho de mecanismo de exibição, que é o caminho relativo de raiz de páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="c1576-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="c1576-138">Observação sobre combinação autorizada e acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="c1576-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="c1576-139">Ele é válido para especificar que uma pasta de páginas que exigem autorização e que especifique que uma página dentro da pasta permite acesso anônimo:</span><span class="sxs-lookup"><span data-stu-id="c1576-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="c1576-140">O inverso, no entanto, não é válido.</span><span class="sxs-lookup"><span data-stu-id="c1576-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="c1576-141">Você não pode declarar uma pasta de páginas para acesso anônimo e, em seguida, especifique uma página dentro dessa pasta que requer autorização:</span><span class="sxs-lookup"><span data-stu-id="c1576-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="c1576-142">Exigir autorização na página privada falhar.</span><span class="sxs-lookup"><span data-stu-id="c1576-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="c1576-143">Quando tanto o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> são aplicadas para a página, o <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> terá precedência e controla o acesso.</span><span class="sxs-lookup"><span data-stu-id="c1576-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1576-144">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c1576-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>
