---
title: Escrever middleware do ASP.NET Core personalizado
author: rick-anderson
description: Saiba como escrever middleware do ASP.NET Core personalizado.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889161"
---
# <a name="write-custom-aspnet-core-middleware"></a>Escrever middleware do ASP.NET Core personalizado

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas. O ASP.NET Core fornece um rico conjunto de componentes de middleware internos, mas em alguns cenários, talvez seja conveniente que você escreva um middleware personalizado.

## <a name="middleware-class"></a>Classe de middleware

O middleware geralmente é encapsulado em uma classe e exposto com um método de extensão. Considere o middleware a seguir, que define a cultura para a solicitação atual de uma cadeia de caracteres de consulta:

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

O código de exemplo anterior é usado para demonstrar a criação de um componente de middleware. Para suporte de localização interna do ASP.NET Core, veja <xref:fundamentals/localization>.

Você pode testar o middleware ao transmitir a cultura. Por exemplo, `http://localhost:7997/?culture=no`.

O código a seguir move o delegado de middleware para uma classe:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

O nome do método `Task` de middleware precisa ser `Invoke`. No ASP.NET Core 2.0 ou posterior, o nome pode ser `Invoke` ou `InvokeAsync`.

::: moniker-end

## <a name="middleware-extension-method"></a>Método de extensão de middleware

O seguinte método de extensão expõe o middleware por meio do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

O código a seguir chama o middleware de `Startup.Configure`:

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

O middleware deve seguir o [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) ao expor suas dependências em seu construtor. O middleware é construído uma vez por *tempo de vida do aplicativo*. Consulte a seção [Dependências por solicitação](#per-request-dependencies) se você precisar compartilhar serviços com middleware dentro de uma solicitação.

Os componentes de middleware podem resolver suas dependências, utilizando a [DI (injeção de dependência)](xref:fundamentals/dependency-injection) por meio de parâmetros do construtor. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) também pode aceitar parâmetros adicionais diretamente.

## <a name="per-request-dependencies"></a>Dependências de pré-solicitação

Uma vez que o middleware é construído durante a inicialização do aplicativo, e não por solicitação, os serviços de tempo de vida *com escopo* usados pelos construtores do middleware não são compartilhados com outros tipos de dependência inseridos durante cada solicitação. Se você tiver que compartilhar um serviço *com escopo* entre seu serviço de middleware e serviços de outros tipos, adicione esses serviços à assinatura do método `Invoke`. O método `Invoke` pode aceitar parâmetros adicionais que são preenchidos pela injeção de dependência:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
