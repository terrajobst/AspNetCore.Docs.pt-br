---
title: Lista segura IP do cliente para o ASP.NET Core
author: damienbod
description: Saiba como filtros de ação ou de Middleware para validar os endereços IP remotos em relação a uma lista de endereços IP aprovados de gravação.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223929"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="fdabc-103">Lista segura IP do cliente para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fdabc-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="fdabc-104">Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fdabc-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="fdabc-105">Este artigo mostra três maneiras de implementar uma lista segura IP (também conhecido como uma lista de permissões) em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fdabc-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="fdabc-106">Você pode usar:</span><span class="sxs-lookup"><span data-stu-id="fdabc-106">You can use:</span></span>

* <span data-ttu-id="fdabc-107">Middleware para verificar o endereço IP remoto de cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="fdabc-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="fdabc-108">Filtros de ação para verificar o endereço IP remoto de solicitações para controladores específicos ou métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="fdabc-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="fdabc-109">Filtros de páginas do Razor para verificar o endereço IP remoto de solicitações de páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="fdabc-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="fdabc-110">Em cada caso, uma cadeia de caracteres que contém os endereços IP de cliente aprovados é armazenada em uma configuração de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fdabc-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="fdabc-111">O middleware ou filtro analisa a cadeia de caracteres em uma lista e verifica se o IP remoto estiver na lista.</span><span class="sxs-lookup"><span data-stu-id="fdabc-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="fdabc-112">Caso contrário, será retornado um código de status HTTP 403 Proibido.</span><span class="sxs-lookup"><span data-stu-id="fdabc-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="fdabc-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fdabc-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="fdabc-114">A lista segura</span><span class="sxs-lookup"><span data-stu-id="fdabc-114">The safelist</span></span>

<span data-ttu-id="fdabc-115">A lista é configurada na *appSettings. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="fdabc-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="fdabc-116">Ele é uma lista delimitada por ponto e vírgula e pode conter endereços IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="fdabc-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="fdabc-117">Middleware</span><span class="sxs-lookup"><span data-stu-id="fdabc-117">Middleware</span></span>

<span data-ttu-id="fdabc-118">O `Configure` método adiciona o middleware e passa a cadeia de caracteres de lista segura para ele em um parâmetro de construtor.</span><span class="sxs-lookup"><span data-stu-id="fdabc-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="fdabc-119">O middleware analisa a cadeia de caracteres em uma matriz e procura o endereço IP remoto na matriz.</span><span class="sxs-lookup"><span data-stu-id="fdabc-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="fdabc-120">Se o endereço IP remoto não for encontrado, o middleware retornará HTTP 401 proibido.</span><span class="sxs-lookup"><span data-stu-id="fdabc-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="fdabc-121">Esse processo de validação é ignorado para solicitações HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="fdabc-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="fdabc-122">Filtro de ação</span><span class="sxs-lookup"><span data-stu-id="fdabc-122">Action filter</span></span>

<span data-ttu-id="fdabc-123">Se você quiser uma lista segura somente para controladores específicos ou métodos de ação, use um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="fdabc-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="fdabc-124">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="fdabc-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="fdabc-125">O filtro de ação é adicionado ao contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="fdabc-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="fdabc-126">O filtro, em seguida, pode ser usado em um método de ação ou controlador.</span><span class="sxs-lookup"><span data-stu-id="fdabc-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="fdabc-127">No aplicativo de exemplo, o filtro é aplicado para o `Get` método.</span><span class="sxs-lookup"><span data-stu-id="fdabc-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="fdabc-128">Portanto, quando você testar o aplicativo, enviando um `Get` solicitação de API, o atributo é validar o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="fdabc-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="fdabc-129">Quando você testa chamando a API com qualquer outro método HTTP, o middleware é validar o IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="fdabc-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="fdabc-130">Filtro de páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="fdabc-130">Razor Pages filter</span></span> 

<span data-ttu-id="fdabc-131">Se você quiser uma lista segura para um aplicativo páginas Razor, use um filtro de páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="fdabc-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="fdabc-132">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="fdabc-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="fdabc-133">Esse filtro é habilitado adicionando-o à coleção de filtros MVC.</span><span class="sxs-lookup"><span data-stu-id="fdabc-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="fdabc-134">Quando você executa o aplicativo e solicita uma página Razor, o filtro de páginas do Razor é validar o IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="fdabc-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fdabc-135">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="fdabc-135">Next steps</span></span>

<span data-ttu-id="fdabc-136">[Saiba mais sobre o Middleware do ASP.NET Core](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="fdabc-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
