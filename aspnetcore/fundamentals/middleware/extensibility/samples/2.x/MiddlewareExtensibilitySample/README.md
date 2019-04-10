---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809365"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a>Amostra de extensibilidade do Middleware do ASP.NET Core

Esta amostra apresenta os cenários descritos em [Ativação de middleware baseada em alocador no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).

O aplicativo de exemplo demonstra o middleware ativado por:

* Convenção. Para obter mais informações sobre a ativação de middleware convencional, consulte o tópico [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/).
* Uma implementação de [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware). A classe [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) padrão ativa o middleware.

As implementações de middleware funcionam de forma idêntica e registram o valor fornecido por um parâmetro de cadeia de caracteres de consulta (`key`). Os middlewares usam um contexto de banco de dados injetado (um serviço com escopo) para registrar o valor de cadeia de caracteres de consulta em um banco de dados em memória.
