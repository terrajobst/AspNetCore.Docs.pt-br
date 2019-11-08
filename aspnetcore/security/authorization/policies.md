---
title: Autorização baseada em políticas no ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar manipuladores de política de autorização para impor requisitos de autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: e3929fb0f45d4ba28f46a6b42b653940de0badb0
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761045"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorização baseada em políticas no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Sob os bastidores, a autorização [baseada em função](xref:security/authorization/roles) e a [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada. Esses blocos de construção dão suporte à expressão de avaliações de autorização no código. O resultado é uma estrutura de autorização mais rica, reutilizável e que pode ser testada.

Uma política de autorização consiste em um ou mais requisitos. Ele é registrado como parte da configuração do serviço de autorização, no método `Startup.ConfigureServices`:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

No exemplo anterior, uma política "AtLeast21" é criada. Ele tem um requisito único&mdash;de uma idade mínima, que é fornecida como um parâmetro para o requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

O serviço primário que determina se a autorização é bem-sucedida é <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

O código anterior realça os dois métodos do [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> é um serviço de marcador sem métodos e o mecanismo para controlar se a autorização é bem-sucedida.

Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> é responsável por verificar se os requisitos são atendidos:
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

A classe <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> é o que o manipulador usa para marcar se os requisitos foram atendidos:

```csharp
 context.Succeed(requirement)
```

O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

O código a seguir mostra um `ConfigureServices`típico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` para autorização.

## <a name="applying-policies-to-mvc-controllers"></a>Aplicando políticas a controladores MVC

Se você estiver usando Razor Pages, consulte [aplicando políticas a Razor Pages](#applying-policies-to-razor-pages) neste documento.

As políticas são aplicadas aos controladores usando o atributo `[Authorize]` com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Aplicando políticas a Razor Pages

As políticas são aplicadas a Razor Pages usando o atributo `[Authorize]` com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

As políticas também podem ser aplicadas a Razor Pages usando uma [Convenção de autorização](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Requisitos

Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de usuário atual. Em nossa política de "AtLeast21", o requisito é um único parâmetro&mdash;a idade mínima. Um requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia. Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos deverão ser aprovados para que a avaliação da política seja realizada com êxito. Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados em uma base **e** .

> [!NOTE]
> Um requisito não precisa ter dados ou propriedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Manipuladores de autorização

Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito. O manipulador de autorização avalia os requisitos em relação a um [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornecido para determinar se o acesso é permitido.

Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers). Um manipulador pode herdar [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), em que `TRequirement` é o requisito a ser manipulado. Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Usar um manipulador para um requisito

<a name="security-authorization-handler-example"></a>

Veja a seguir um exemplo de uma relação um-para-um na qual um manipulador de idade mínima utiliza um único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

O código anterior determina se a entidade de usuário atual tem uma declaração de data de nascimento que foi emitida por um emissor conhecido e confiável. A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída é retornada. Quando uma declaração está presente, a idade do usuário é calculada. Se o usuário atender à idade mínima definida pelo requisito, a autorização será considerada bem-sucedida. Quando a autorização é bem-sucedida, o `context.Succeed` é invocado com o requisito atendido como seu único parâmetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Usar um manipulador para vários requisitos

Veja a seguir um exemplo de uma relação um-para-muitos na qual um manipulador de permissão pode lidar com três tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém os requisitos não marcados como êxito. Para um requisito de `ReadPermission`, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado. No caso de um requisito de `EditPermission` ou `DeletePermission`, ele deve ser um proprietário para acessar o recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro de manipulador

Os manipuladores são registrados na coleção de serviços durante a configuração. Por exemplo:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

O código anterior registra `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Os manipuladores podem ser registrados usando qualquer tempo de vida de [serviço](xref:fundamentals/dependency-injection#service-lifetimes)interno.

## <a name="what-should-a-handler-return"></a>O que um manipulador deve retornar?

Observe que o método `Handle` no [exemplo do manipulador](#security-authorization-handler-example) não retorna nenhum valor. Como é indicado um status de êxito ou falha?

* Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)`, passando o requisito que foi validado com êxito.

* Um manipulador não precisa lidar com falhas geralmente, já que outros manipuladores para o mesmo requisito podem ser bem-sucedidos.

* Para garantir a falha, mesmo que outros manipuladores de requisitos tenham êxito, chame `context.Fail`.

Se um manipulador chamar `context.Succeed` ou `context.Fail`, todos os outros manipuladores ainda serão chamados. Isso permite que os requisitos produzam efeitos colaterais, como registro em log, que ocorre mesmo se outro manipulador tiver validado ou reprovado com êxito um requisito. Quando definido como `false`, a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Core 1,1 e posterior) faz circuitos curtos na execução de manipuladores quando `context.Fail` é chamado. `InvokeHandlersAfterFailure` usa como padrão `true`e, nesse caso, todos os manipuladores são chamados.

> [!NOTE]
> Os manipuladores de autorização são chamados mesmo se a autenticação falhar.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Por que desejo vários manipuladores para um requisito?

Nos casos em que você deseja que a avaliação seja em um **ou** base, implemente vários manipuladores para um único requisito. Por exemplo, a Microsoft tem portas que só são abertas com cartões-chave. Se você deixar o cartão-chave em casa, o recepcionista imprime um adesivo temporário e abre a porta para você. Nesse cenário, você teria um único requisito, *BuildingEntry*, mas vários manipuladores, cada um examinando um único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Verifique se ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Se um dos manipuladores for executado com sucesso quando uma política avaliar o `BuildingEntryRequirement`, a avaliação da política será realizada com sucesso.

## <a name="using-a-func-to-fulfill-a-policy"></a>Usando um Func para atender a uma política

Pode haver situações em que o cumprimento de uma política é simples de expressar no código. É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua política com o `RequireAssertion` Policy Builder.

Por exemplo, a `BadgeEntryHandler` anterior poderia ser reescrita da seguinte maneira:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Acessando o contexto de solicitação MVC em manipuladores

O método de `HandleRequirementAsync` que você implementa em um manipulador de autorização tem dois parâmetros: um `AuthorizationHandlerContext` e o `TRequirement` que você está lidando. Estruturas como MVC ou Jabbr são livres para adicionar qualquer objeto à propriedade `Resource` no `AuthorizationHandlerContext` para passar informações extras.

Por exemplo, o MVC passa uma instância de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) na propriedade `Resource`. Essa propriedade fornece acesso a `HttpContext`, `RouteData`e tudo o mais fornecido pelo MVC e Razor Pages.

O uso da propriedade `Resource` é específico da estrutura. O uso de informações na propriedade `Resource` limita suas políticas de autorização a estruturas específicas. Você deve converter a propriedade `Resource` usando a palavra-chave `is` e, em seguida, confirmar se a conversão foi bem-sucedida para garantir que seu código não falhe com um `InvalidCastException` quando executado em outras estruturas:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

Sob os bastidores, a autorização [baseada em função](xref:security/authorization/roles) e a [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisitos e uma política pré-configurada. Esses blocos de construção dão suporte à expressão de avaliações de autorização no código. O resultado é uma estrutura de autorização mais rica, reutilizável e que pode ser testada.

Uma política de autorização consiste em um ou mais requisitos. Ele é registrado como parte da configuração do serviço de autorização, no método `Startup.ConfigureServices`:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

No exemplo anterior, uma política "AtLeast21" é criada. Ele tem um requisito único&mdash;de uma idade mínima, que é fornecida como um parâmetro para o requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

O serviço primário que determina se a autorização é bem-sucedida é <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

O código anterior realça os dois métodos do [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> é um serviço de marcador sem métodos e o mecanismo para controlar se a autorização é bem-sucedida.

Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> é responsável por verificar se os requisitos são atendidos:
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

A classe <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> é o que o manipulador usa para marcar se os requisitos foram atendidos:

```csharp
 context.Succeed(requirement)
```

O código a seguir mostra a implementação padrão simplificada (e anotada com comentários) do serviço de autorização:

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

O código a seguir mostra um `ConfigureServices`típico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` para autorização.

## <a name="applying-policies-to-mvc-controllers"></a>Aplicando políticas a controladores MVC

Se você estiver usando Razor Pages, consulte [aplicando políticas a Razor Pages](#applying-policies-to-razor-pages) neste documento.

As políticas são aplicadas aos controladores usando o atributo `[Authorize]` com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Aplicando políticas a Razor Pages

As políticas são aplicadas a Razor Pages usando o atributo `[Authorize]` com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

As políticas também podem ser aplicadas a Razor Pages usando uma [Convenção de autorização](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Requisitos

Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de usuário atual. Em nossa política de "AtLeast21", o requisito é um único parâmetro&mdash;a idade mínima. Um requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazia. Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Se uma política de autorização contiver vários requisitos de autorização, todos os requisitos deverão ser aprovados para que a avaliação da política seja realizada com êxito. Em outras palavras, vários requisitos de autorização adicionados a uma única política de autorização são tratados em uma base **e** .

> [!NOTE]
> Um requisito não precisa ter dados ou propriedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Manipuladores de autorização

Um manipulador de autorização é responsável pela avaliação das propriedades de um requisito. O manipulador de autorização avalia os requisitos em relação a um [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornecido para determinar se o acesso é permitido.

Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers). Um manipulador pode herdar [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), em que `TRequirement` é o requisito a ser manipulado. Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Usar um manipulador para um requisito

<a name="security-authorization-handler-example"></a>

Veja a seguir um exemplo de uma relação um-para-um na qual um manipulador de idade mínima utiliza um único requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

O código anterior determina se a entidade de usuário atual tem uma declaração de data de nascimento que foi emitida por um emissor conhecido e confiável. A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída é retornada. Quando uma declaração está presente, a idade do usuário é calculada. Se o usuário atender à idade mínima definida pelo requisito, a autorização será considerada bem-sucedida. Quando a autorização é bem-sucedida, o `context.Succeed` é invocado com o requisito atendido como seu único parâmetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Usar um manipulador para vários requisitos

Veja a seguir um exemplo de uma relação um-para-muitos na qual um manipulador de permissão pode lidar com três tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém os requisitos não marcados como êxito. Para um requisito de `ReadPermission`, o usuário deve ser um proprietário ou um patrocinador para acessar o recurso solicitado. No caso de um requisito de `EditPermission` ou `DeletePermission`, ele deve ser um proprietário para acessar o recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro de manipulador

Os manipuladores são registrados na coleção de serviços durante a configuração. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

O código anterior registra `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Os manipuladores podem ser registrados usando qualquer tempo de vida de [serviço](xref:fundamentals/dependency-injection#service-lifetimes)interno.

## <a name="what-should-a-handler-return"></a>O que um manipulador deve retornar?

Observe que o método `Handle` no [exemplo do manipulador](#security-authorization-handler-example) não retorna nenhum valor. Como é indicado um status de êxito ou falha?

* Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)`, passando o requisito que foi validado com êxito.

* Um manipulador não precisa lidar com falhas geralmente, já que outros manipuladores para o mesmo requisito podem ser bem-sucedidos.

* Para garantir a falha, mesmo que outros manipuladores de requisitos tenham êxito, chame `context.Fail`.

Se um manipulador chamar `context.Succeed` ou `context.Fail`, todos os outros manipuladores ainda serão chamados. Isso permite que os requisitos produzam efeitos colaterais, como registro em log, que ocorre mesmo se outro manipulador tiver validado ou reprovado com êxito um requisito. Quando definido como `false`, a propriedade [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponível em ASP.NET Core 1,1 e posterior) faz circuitos curtos na execução de manipuladores quando `context.Fail` é chamado. `InvokeHandlersAfterFailure` usa como padrão `true`e, nesse caso, todos os manipuladores são chamados.

> [!NOTE]
> Os manipuladores de autorização são chamados mesmo se a autenticação falhar.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Por que desejo vários manipuladores para um requisito?

Nos casos em que você deseja que a avaliação seja em um **ou** base, implemente vários manipuladores para um único requisito. Por exemplo, a Microsoft tem portas que só são abertas com cartões-chave. Se você deixar o cartão-chave em casa, o recepcionista imprime um adesivo temporário e abre a porta para você. Nesse cenário, você teria um único requisito, *BuildingEntry*, mas vários manipuladores, cada um examinando um único requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Verifique se ambos os manipuladores estão [registrados](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Se um dos manipuladores for executado com sucesso quando uma política avaliar o `BuildingEntryRequirement`, a avaliação da política será realizada com sucesso.

## <a name="using-a-func-to-fulfill-a-policy"></a>Usando um Func para atender a uma política

Pode haver situações em que o cumprimento de uma política é simples de expressar no código. É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar sua política com o `RequireAssertion` Policy Builder.

Por exemplo, a `BadgeEntryHandler` anterior poderia ser reescrita da seguinte maneira:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Acessando o contexto de solicitação MVC em manipuladores

O método de `HandleRequirementAsync` que você implementa em um manipulador de autorização tem dois parâmetros: um `AuthorizationHandlerContext` e o `TRequirement` que você está lidando. Estruturas como MVC ou Jabbr são livres para adicionar qualquer objeto à propriedade `Resource` no `AuthorizationHandlerContext` para passar informações extras.

Por exemplo, o MVC passa uma instância de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) na propriedade `Resource`. Essa propriedade fornece acesso a `HttpContext`, `RouteData`e tudo o mais fornecido pelo MVC e Razor Pages.

O uso da propriedade `Resource` é específico da estrutura. O uso de informações na propriedade `Resource` limita suas políticas de autorização a estruturas específicas. Você deve converter a propriedade `Resource` usando a palavra-chave `is` e, em seguida, confirmar se a conversão foi bem-sucedida para garantir que seu código não falhe com um `InvalidCastException` quando executado em outras estruturas:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end