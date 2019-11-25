---
title: Acessar o HttpContext no ASP.NET Core
author: coderandhiker
description: Saiba como acessar o HttpContext no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: 0bf40f9cd2554f5ba01ccc06001fa4f1940d51a5
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289046"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="dd492-103">Acessar o HttpContext no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd492-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="dd492-104">Os aplicativos ASP.NET Core acessam o `HttpContext` por meio da interface [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) e da implementação padrão do [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span><span class="sxs-lookup"><span data-stu-id="dd492-104">ASP.NET Core apps access the `HttpContext` through the [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) interface and its default implementation [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span></span> <span data-ttu-id="dd492-105">Só é necessário usar o `IHttpContextAccessor` quando você precisar acessar o `HttpContext` em um serviço.</span><span class="sxs-lookup"><span data-stu-id="dd492-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="dd492-106">Usar o HttpContext de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="dd492-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="dd492-107">O [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) das Razor Pages expõe a propriedade [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext):</span><span class="sxs-lookup"><span data-stu-id="dd492-107">The Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) exposes the [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="dd492-108">Usar o HttpContext de uma exibição do Razor</span><span class="sxs-lookup"><span data-stu-id="dd492-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="dd492-109">As exibições do Razor expõem o `HttpContext` diretamente por meio de uma propriedade [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) na exibição.</span><span class="sxs-lookup"><span data-stu-id="dd492-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) property on the view.</span></span> <span data-ttu-id="dd492-110">O exemplo a seguir recupera o nome de usuário atual em um aplicativo de Intranet usando a Autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="dd492-110">The following example retrieves the current username in an Intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="dd492-111">Usar o HttpContext de um controlador</span><span class="sxs-lookup"><span data-stu-id="dd492-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="dd492-112">Os controladores expõem a propriedade [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext):</span><span class="sxs-lookup"><span data-stu-id="dd492-112">Controllers expose the [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="dd492-113">Usar o HttpContext de middleware</span><span class="sxs-lookup"><span data-stu-id="dd492-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="dd492-114">Ao trabalhar com componentes personalizados de middleware, o `HttpContext` é passado para o método `Invoke` ou `InvokeAsync` e pode ser acessado quando o middleware for configurado:</span><span class="sxs-lookup"><span data-stu-id="dd492-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="dd492-115">Usar o HttpContext de componentes personalizados</span><span class="sxs-lookup"><span data-stu-id="dd492-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="dd492-116">Para outras estruturas e componentes personalizados que exigem acesso ao `HttpContext`, a abordagem recomendada é registrar uma dependência usando o contêiner integrado de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dd492-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dd492-117">O contêiner de injeção de dependência fornece o `IHttpContextAccessor` para todas as classes que o declaram como uma dependência em seus construtores.</span><span class="sxs-lookup"><span data-stu-id="dd492-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors.</span></span>

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

<span data-ttu-id="dd492-118">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="dd492-118">In the following example:</span></span>

* <span data-ttu-id="dd492-119">o `UserRepository` declara sua dependência no `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="dd492-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="dd492-120">A dependência é fornecida quando a injeção de dependência resolve a cadeia de dependências e cria uma instância do `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="dd492-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="dd492-121">Acesso a HttpContext de um thread em segundo plano</span><span class="sxs-lookup"><span data-stu-id="dd492-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="dd492-122">`HttpContext` não é thread-safe.</span><span class="sxs-lookup"><span data-stu-id="dd492-122">`HttpContext` is not thread-safe.</span></span> <span data-ttu-id="dd492-123">Ler ou gravar propriedades de `HttpContext` fora do processamento de uma solicitação pode resultar em um `NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="dd492-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a `NullReferenceException`.</span></span>

> [!NOTE]
> <span data-ttu-id="dd492-124">Usar `HttpContext` fora do processamento de uma solicitação geralmente resulta em um `NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="dd492-124">Using `HttpContext` outside of processing a request often results in a `NullReferenceException`.</span></span> <span data-ttu-id="dd492-125">Se seu aplicativo gera `NullReferenceException`s esporádicos, revise as partes do código que iniciam o processamento em segundo plano ou que continuam o processamento depois que uma solicitação é concluída.</span><span class="sxs-lookup"><span data-stu-id="dd492-125">If your app generates sporadic `NullReferenceException`s , review parts of the code that start background processing, or that continue processing after a request completes.</span></span> <span data-ttu-id="dd492-126">Procure erros, como a definição de um método de controlador como `async void`.</span><span class="sxs-lookup"><span data-stu-id="dd492-126">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="dd492-127">Para executar com segurança o trabalho em segundo plano com os dados de `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="dd492-127">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="dd492-128">Copie os dados necessários durante o processamento da solicitação.</span><span class="sxs-lookup"><span data-stu-id="dd492-128">Copy the required data during request processing.</span></span>
* <span data-ttu-id="dd492-129">Passe os dados copiados para uma tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="dd492-129">Pass the copied data to a background task.</span></span>

<span data-ttu-id="dd492-130">Para evitar o código não seguro, nunca passe o `HttpContext` em um método que realiza o trabalho em segundo plano. Em vez disso, passe os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="dd492-130">To avoid unsafe code, never pass the `HttpContext` into a method that does background work - pass the data you need instead.</span></span>

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        // Starts sending an email, but doesn't wait for it to complete
        _ = SendEmailCore(correlationId);
        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        // send the email
    }
}
