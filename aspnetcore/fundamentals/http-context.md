---
title: Acessar o HttpContext no ASP.NET Core
author: coderandhiker
description: Saiba como acessar o HttpContext no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: babc637cdec8590ac14f7924c17e862e5b2f6a81
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428480"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="57f7c-103">Acessar o HttpContext no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57f7c-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="57f7c-104">Os aplicativos ASP.NET Core acessam o `HttpContext` por meio da interface [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) e da implementação padrão do [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span><span class="sxs-lookup"><span data-stu-id="57f7c-104">ASP.NET Core apps access the `HttpContext` through the [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) interface and its default implementation [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span></span> <span data-ttu-id="57f7c-105">Só é necessário usar o `IHttpContextAccessor` quando você precisar acessar o `HttpContext` em um serviço.</span><span class="sxs-lookup"><span data-stu-id="57f7c-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="57f7c-106">Usar o HttpContext de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="57f7c-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="57f7c-107">O [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) das Razor Pages expõe a propriedade [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext):</span><span class="sxs-lookup"><span data-stu-id="57f7c-107">The Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) exposes the [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

::: moniker-end

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="57f7c-108">Usar o HttpContext de uma exibição do Razor</span><span class="sxs-lookup"><span data-stu-id="57f7c-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="57f7c-109">As exibições do Razor expõem o `HttpContext` diretamente por meio de uma propriedade [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) na exibição.</span><span class="sxs-lookup"><span data-stu-id="57f7c-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) property on the view.</span></span> <span data-ttu-id="57f7c-110">O exemplo a seguir recupera o nome de usuário atual em um aplicativo de Intranet usando a Autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="57f7c-110">The following example retrieves the current username in an Intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="57f7c-111">Usar o HttpContext de um controlador</span><span class="sxs-lookup"><span data-stu-id="57f7c-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="57f7c-112">Os controladores expõem a propriedade [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext):</span><span class="sxs-lookup"><span data-stu-id="57f7c-112">Controllers expose the [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;
        // Do something with the PathBase.

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="57f7c-113">Usar o HttpContext de middleware</span><span class="sxs-lookup"><span data-stu-id="57f7c-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="57f7c-114">Ao trabalhar com componentes personalizados de middleware, o `HttpContext` é passado para o método `Invoke` ou `InvokeAsync` e pode ser acessado quando o middleware for configurado:</span><span class="sxs-lookup"><span data-stu-id="57f7c-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="57f7c-115">Usar o HttpContext de componentes personalizados</span><span class="sxs-lookup"><span data-stu-id="57f7c-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="57f7c-116">Para outras estruturas e componentes personalizados que exigem acesso ao `HttpContext`, a abordagem recomendada é registrar uma dependência usando o contêiner integrado de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="57f7c-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="57f7c-117">O contêiner de injeção de dependência fornece o `IHttpContextAccessor` para todas as classes que o declaram como uma dependência em seus construtores.</span><span class="sxs-lookup"><span data-stu-id="57f7c-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors.</span></span>

::: moniker range=">= aspnetcore-2.1"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc();
     services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="57f7c-118">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="57f7c-118">In the following example:</span></span>

* <span data-ttu-id="57f7c-119">o `UserRepository` declara sua dependência no `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="57f7c-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="57f7c-120">A dependência é fornecida quando a injeção de dependência resolve a cadeia de dependências e cria uma instância do `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="57f7c-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```
