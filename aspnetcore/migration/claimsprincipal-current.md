---
title: Migrar de ClaimsPrincipal. Current
author: mjrousos
description: Saiba como migrar para longe de ClaimsPrincipal. Current para recuperar as declarações no ASP.NET Core e a identidade do usuário autenticado atual.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: 526cc3cf3a58a656e2a1b162cfaccacc7694dc51
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894783"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrar de ClaimsPrincipal. Current

Em projetos do ASP.NET 4.x, era comum usar [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) recuperar atual autenticado a identidade do usuário e as declarações. No ASP.NET Core, essa propriedade não está definida. Código dependia ele precisa ser atualizado para obter a identidade do usuário autenticado atual por meio de um modo diferente.

## <a name="context-specific-data-instead-of-static-data"></a>Dados específicos ao contexto em vez de dados estáticos

Ao usar o ASP.NET Core, os valores de ambos `ClaimsPrincipal.Current` e `Thread.CurrentPrincipal` não estão definidas. Essas propriedades representam o estado estático, que normalmente evita o ASP.NET Core. Em vez disso, arquitetura do ASP.NET Core é recuperar dependências (como a atual identidade do usuário) de coleções de contexto específico de serviço (usando seu [injeção de dependência](xref:fundamentals/dependency-injection) modelo (DI)). Além disso, `Thread.CurrentPrincipal` é o thread estático, portanto, ele não pode persistir as alterações em alguns cenários assíncronos (e `ClaimsPrincipal.Current` apenas chama `Thread.CurrentPrincipal` por padrão).

Para entender as classificações do thread de problemas de membros estáticos podem levar a cenários assíncronos, considere o seguinte trecho de código:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

O código do exemplo anterior define `Thread.CurrentPrincipal` e verifica seu valor antes e depois de aguardar uma chamada assíncrona. `Thread.CurrentPrincipal` é específico para o *thread* no qual ele é definido e o método provavelmente retomar a execução em um thread diferente após o await. Consequentemente, `Thread.CurrentPrincipal` está presente quando ele é verificado pela primeira vez, mas é nulo após a chamada para `await Task.Yield()`.

Obtendo a atual identidade do usuário da coleção de serviço de injeção de dependência do aplicativo é mais testável, também, desde que as identidades de teste podem ser injetadas com facilidade.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Recuperar o usuário atual em um aplicativo ASP.NET Core

Há várias opções para recuperar o atual usuário autenticado `ClaimsPrincipal` no ASP.NET Core no lugar de `ClaimsPrincipal.Current`:

* **ControllerBase.User**. Controladores do MVC podem acessar o usuário atual autenticado com suas [usuário](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) propriedade.
* **HttpContext.User**. Componentes com acesso ao atual `HttpContext` (por exemplo, middleware) pode obter o usuário atual `ClaimsPrincipal` partir [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Passada pelo chamador**. Bibliotecas sem acesso ao atual `HttpContext` muitas vezes são chamados de controladores ou componentes de middleware e pode ter a atual identidade do usuário passada como um argumento.
* **IHttpContextAccessor**. O projeto que está sendo migrado para o ASP.NET Core pode ser muito grande para passar facilmente a atual identidade do usuário para todos os locais necessários. Nesses casos, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) pode ser usado como uma solução alternativa. `IHttpContextAccessor` é capaz de acessar atual `HttpContext` (se houver). Uma solução de curto prazo para obter a identidade do usuário atual no código que ainda não foi atualizado para trabalhar com a arquitetura de orientado a DI do ASP.NET Core seria:

  * Tornar `IHttpContextAccessor` disponíveis no contêiner de injeção de dependência chamando [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) em `Startup.ConfigureServices`.
  * Obtenha uma instância de `IHttpContextAccessor` durante a inicialização e armazená-lo em uma variável estática. A instância é disponibilizada para o código que anteriormente estava recuperando o usuário atual de uma propriedade estática.
  * Recuperar o usuário atual `ClaimsPrincipal` usando `HttpContextAccessor.HttpContext?.User`. Se esse código é usado fora do contexto de uma solicitação HTTP, o `HttpContext` é nulo.

O final de opção, usando um `IHttpContextAccessor` instância armazenada em uma variável estática, é contrary aos princípios do ASP.NET Core (preferindo dependências injetadas dependências estáticas). Planejar recuperar eventualmente `IHttpContextAccessor` instâncias de injeção de dependência em vez disso. Um auxiliar estático pode ser uma ponte útil, no entanto, quando a migração de grandes aplicativos ASP.NET existentes que estavam usando previamente `ClaimsPrincipal.Current`.
