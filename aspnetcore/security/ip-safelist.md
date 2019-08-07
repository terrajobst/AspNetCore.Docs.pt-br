---
title: IP do cliente da assafe para ASP.NET Core
author: damienbod
description: Saiba como escrever filtros de middleware ou de ação para validar endereços IP remotos em uma lista de endereços IP aprovados.
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
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="64512-103">IP do cliente da assafe para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64512-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="64512-104">Por [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="64512-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="64512-105">Este artigo mostra três maneiras de implementar uma lista de permissões de IP (também conhecida como whitelist) em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="64512-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="64512-106">Você pode usar:</span><span class="sxs-lookup"><span data-stu-id="64512-106">You can use:</span></span>

* <span data-ttu-id="64512-107">Middleware para verificar o endereço IP remoto de cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="64512-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="64512-108">Filtros de ação para verificar o endereço IP remoto de solicitações para controladores ou métodos de ação específicos.</span><span class="sxs-lookup"><span data-stu-id="64512-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="64512-109">Razor Pages filtros para verificar o endereço IP remoto de solicitações para páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="64512-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="64512-110">Em cada caso, uma cadeia de caracteres contendo endereços IP de cliente aprovados é armazenada em uma configuração de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="64512-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="64512-111">O middleware ou o filtro analisa a cadeia de caracteres em uma lista e verifica se o IP remoto está na lista.</span><span class="sxs-lookup"><span data-stu-id="64512-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="64512-112">Caso contrário, um código de status HTTP 403 proibido será retornado.</span><span class="sxs-lookup"><span data-stu-id="64512-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="64512-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="64512-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="64512-114">A SafeList</span><span class="sxs-lookup"><span data-stu-id="64512-114">The safelist</span></span>

<span data-ttu-id="64512-115">A lista é configurada no arquivo *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="64512-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="64512-116">É uma lista delimitada por ponto-e-vírgula e pode conter endereços IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="64512-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="64512-117">Middleware</span><span class="sxs-lookup"><span data-stu-id="64512-117">Middleware</span></span>

<span data-ttu-id="64512-118">O `Configure` método adiciona o middleware e passa a cadeia de caracteres de SafeList para ele em um parâmetro de construtor.</span><span class="sxs-lookup"><span data-stu-id="64512-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="64512-119">O middleware analisa a cadeia de caracteres em uma matriz e procura o endereço IP remoto na matriz.</span><span class="sxs-lookup"><span data-stu-id="64512-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="64512-120">Se o endereço IP remoto não for encontrado, o middleware retornará HTTP 401 proibido.</span><span class="sxs-lookup"><span data-stu-id="64512-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="64512-121">Esse processo de validação é ignorado para solicitações HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="64512-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="64512-122">Filtro de ação</span><span class="sxs-lookup"><span data-stu-id="64512-122">Action filter</span></span>

<span data-ttu-id="64512-123">Se você quiser uma forma segura somente para controladores específicos ou métodos de ação, use um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="64512-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="64512-124">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="64512-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="64512-125">O filtro de ação é adicionado ao contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="64512-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="64512-126">O filtro pode ser usado em um controlador ou método de ação.</span><span class="sxs-lookup"><span data-stu-id="64512-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="64512-127">No aplicativo de exemplo, o filtro é aplicado ao `Get` método.</span><span class="sxs-lookup"><span data-stu-id="64512-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="64512-128">Então, quando você testa o aplicativo enviando uma `Get` solicitação de API, o atributo está validando o endereço IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="64512-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="64512-129">Quando você testa chamando a API com qualquer outro método HTTP, o middleware está validando o IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="64512-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="64512-130">Filtro de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="64512-130">Razor Pages filter</span></span> 

<span data-ttu-id="64512-131">Se você quiser uma assafe para um aplicativo Razor Pages, use um filtro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="64512-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="64512-132">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="64512-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="64512-133">Esse filtro é habilitado adicionando-o à coleção de filtros MVC.</span><span class="sxs-lookup"><span data-stu-id="64512-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="64512-134">Quando você executa o aplicativo e solicita uma página Razor, o filtro de Razor Pages está validando o IP do cliente.</span><span class="sxs-lookup"><span data-stu-id="64512-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="64512-135">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="64512-135">Next steps</span></span>

<span data-ttu-id="64512-136">[Saiba mais sobre o middleware ASP.NET Core](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="64512-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
