---
title: Acessar o HttpContext no ASP.NET Core
author: coderandhiker
description: Saiba como acessar o HttpContext no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: 5974e3e66dab3398ecf2324fbb82a9c5636f70de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74778733"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Acessar o HttpContext no ASP.NET Core

ASP.NET Core aplicativos acessam `HttpContext` por meio da interface <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> e seu <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>de implementação padrão. Só é necessário usar o `IHttpContextAccessor` quando você precisar acessar o `HttpContext` em um serviço.

## <a name="use-httpcontext-from-razor-pages"></a>Usar o HttpContext de Razor Pages

O Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Usar o HttpContext de uma exibição do Razor

As exibições do Razor expõem o `HttpContext` diretamente por meio de uma propriedade [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) na exibição. O exemplo a seguir recupera o nome de usuário atual em um aplicativo de intranet usando a autenticação do Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Usar o HttpContext de um controlador

Os controladores expõem a propriedade [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>Usar o HttpContext de middleware

Ao trabalhar com componentes personalizados de middleware, o `HttpContext` é passado para o método `Invoke` ou `InvokeAsync` e pode ser acessado quando o middleware for configurado:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Usar o HttpContext de componentes personalizados

Para outras estruturas e componentes personalizados que exigem acesso ao `HttpContext`, a abordagem recomendada é registrar uma dependência usando o contêiner integrado de [injeção de dependência](xref:fundamentals/dependency-injection). O contêiner de injeção de dependência fornece a `IHttpContextAccessor` a todas as classes que a declaram como uma dependência em seus construtores:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

No exemplo a seguir:

* o `UserRepository` declara sua dependência no `IHttpContextAccessor`.
* A dependência é fornecida quando a injeção de dependência resolve a cadeia de dependências e cria uma instância do `UserRepository`.

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

## <a name="httpcontext-access-from-a-background-thread"></a>Acesso a HttpContext de um thread em segundo plano

`HttpContext` não é thread-safe. Ler ou gravar propriedades de `HttpContext` fora do processamento de uma solicitação pode resultar em um <xref:System.NullReferenceException>.

> [!NOTE]
> Se seu aplicativo gerar erros esporádicos de `NullReferenceException`, examine partes do código que iniciam o processamento em segundo plano ou que continuam processando após a conclusão de uma solicitação. Procure erros, como a definição de um método de controlador como `async void`.

Para executar com segurança o trabalho em segundo plano com os dados de `HttpContext`:

* Copie os dados necessários durante o processamento da solicitação.
* Passe os dados copiados para uma tarefa em segundo plano.

Para evitar código não seguro, nunca passe o `HttpContext` para um método que executa o trabalho em segundo plano. Em vez disso, passe os dados necessários. No exemplo a seguir, `SendEmailCore` é chamado para começar a enviar um email. O `correlationId` é passado para `SendEmailCore`, não para o `HttpContext`. A execução de código não aguarda a conclusão do `SendEmailCore`:

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
