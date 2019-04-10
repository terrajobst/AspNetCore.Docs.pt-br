---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809365"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a><span data-ttu-id="d7fe1-101">Amostra de extensibilidade do Middleware do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7fe1-101">ASP.NET Core Middleware Extensibility Sample</span></span>

<span data-ttu-id="d7fe1-102">Esta amostra apresenta os cenários descritos em [Ativação de middleware baseada em alocador no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span><span class="sxs-lookup"><span data-stu-id="d7fe1-102">This sample demonstrates the scenarios described in [Factory-based middleware activation in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span></span>

<span data-ttu-id="d7fe1-103">O aplicativo de exemplo demonstra o middleware ativado por:</span><span class="sxs-lookup"><span data-stu-id="d7fe1-103">The sample app demonstrates middleware activated by:</span></span>

* <span data-ttu-id="d7fe1-104">Convenção.</span><span class="sxs-lookup"><span data-stu-id="d7fe1-104">Convention.</span></span> <span data-ttu-id="d7fe1-105">Para obter mais informações sobre a ativação de middleware convencional, consulte o tópico [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/).</span><span class="sxs-lookup"><span data-stu-id="d7fe1-105">For more information on conventional middleware activation, see the [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) topic.</span></span>
* <span data-ttu-id="d7fe1-106">Uma implementação de [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware).</span><span class="sxs-lookup"><span data-stu-id="d7fe1-106">An [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementation.</span></span> <span data-ttu-id="d7fe1-107">A classe [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) padrão ativa o middleware.</span><span class="sxs-lookup"><span data-stu-id="d7fe1-107">The default [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) class activates the middleware.</span></span>

<span data-ttu-id="d7fe1-108">As implementações de middleware funcionam de forma idêntica e registram o valor fornecido por um parâmetro de cadeia de caracteres de consulta (`key`).</span><span class="sxs-lookup"><span data-stu-id="d7fe1-108">The middleware implementations function identically and record the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="d7fe1-109">Os middlewares usam um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor de cadeia de caracteres de consulta em um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="d7fe1-109">The middlewares use an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>
