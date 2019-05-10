---
title: Injeção de dependência em manipuladores de requisito no ASP.NET Core
author: rick-anderson
description: Saiba como injetar manipuladores de requisito de autorização em um aplicativo ASP.NET Core usando a injeção de dependência.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 71d563e11d308a95c08e6d012d3a071f4697d2de
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896363"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="b5fef-103">Injeção de dependência em manipuladores de requisito no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5fef-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="b5fef-104">[Manipuladores de autorização devem ser registrados](xref:security/authorization/policies#handler-registration) na coleção durante a configuração do serviço (usando [injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="b5fef-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="b5fef-105">Suponha que você tenha um repositório de regras que você queira avaliar dentro de um manipulador de autorização e esse repositório foi registrado na coleção de serviço.</span><span class="sxs-lookup"><span data-stu-id="b5fef-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="b5fef-106">A autorização será resolver e inseri-los em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="b5fef-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="b5fef-107">Por exemplo, se você quiser usar o ASP. NET do log a infraestrutura que você deseja injetar `ILoggerFactory` em seu manipulador.</span><span class="sxs-lookup"><span data-stu-id="b5fef-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="b5fef-108">Um manipulador desse tipo pode parecer com:</span><span class="sxs-lookup"><span data-stu-id="b5fef-108">Such a handler might look like:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="b5fef-109">Você deve registrar o manipulador com `services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="b5fef-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="b5fef-110">Uma instância do manipulador será criado quando o aplicativo for iniciado e será de DI injetar registrado `ILoggerFactory` em seu construtor.</span><span class="sxs-lookup"><span data-stu-id="b5fef-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="b5fef-111">Manipuladores que usam o Entity Framework não devem ser registrados como singletons.</span><span class="sxs-lookup"><span data-stu-id="b5fef-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
