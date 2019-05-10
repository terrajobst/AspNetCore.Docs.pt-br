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
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Injeção de dependência em manipuladores de requisito no ASP.NET Core

<a name="security-authorization-di"></a>

[Manipuladores de autorização devem ser registrados](xref:security/authorization/policies#handler-registration) na coleção durante a configuração do serviço (usando [injeção de dependência](xref:fundamentals/dependency-injection)).

Suponha que você tenha um repositório de regras que você queira avaliar dentro de um manipulador de autorização e esse repositório foi registrado na coleção de serviço. A autorização será resolver e inseri-los em seu construtor.

Por exemplo, se você quiser usar o ASP. NET do log a infraestrutura que você deseja injetar `ILoggerFactory` em seu manipulador. Um manipulador desse tipo pode parecer com:

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

Você deve registrar o manipulador com `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Uma instância do manipulador será criado quando o aplicativo for iniciado e será de DI injetar registrado `ILoggerFactory` em seu construtor.

> [!NOTE]
> Manipuladores que usam o Entity Framework não devem ser registrados como singletons.
