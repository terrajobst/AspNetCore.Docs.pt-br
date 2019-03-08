---
title: Autorização baseada em política no ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar manipuladores de política de autorização para impor requisitos de autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2017
uid: security/authorization/policies
ms.openlocfilehash: c2bc626b2dd341dda878a151def6b405884357d7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665396"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorização baseada em política no ASP.NET Core

Nos bastidores, [autorização baseada em funções](xref:security/authorization/roles) e [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisito e uma política previamente configurada. Esses blocos de construção dão suporte a expressão de avaliações de autorização no código. O resultado é uma estrutura de autorização mais sofisticados, reutilizáveis e testáveis.

Uma política de autorização consiste em um ou mais requisitos. Ele é registrado como parte da configuração do serviço de autorização no `Startup.ConfigureServices` método:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63,72)]

No exemplo anterior, uma política de "AtLeast21" é criada. Ele tem um requisito&mdash;da idade mínima, que é fornecida como um parâmetro para o requisito.

As políticas são aplicadas usando o `[Authorize]` atributo com o nome da política. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="requirements"></a>Requisitos

Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de segurança do usuário atual. Em nossa política de "AtLeast21", o requisito é um único parâmetro&mdash;a idade mínima. Implementa um requisito [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazio. Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Se uma política de autorização contém vários requisitos de autorização, devem passar todos os requisitos para que a avaliação da política seja bem-sucedida. Em outras palavras, os vários requisitos de autorização adicionados a uma política de autorização única são tratados em um **AND** base.

> [!NOTE]
> Um requisito não precisa ter dados ou propriedades.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Manipuladores de autorização

Um manipulador de autorização é responsável para a avaliação das propriedades de um requisito. O manipulador de autorização avalia os requisitos em relação a um fornecido [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) para determinar se o acesso é permitido.

Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers). Um manipulador pode herdar [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), onde `TRequirement` é o requisito para ser tratada. Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.

### <a name="use-a-handler-for-one-requirement"></a>Usar um manipulador para um requisito

<a name="security-authorization-handler-example"></a>

Este é um exemplo de uma relação um para um em que um manipulador de idade mínima utiliza um requisito:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

O código anterior determina se a entidade de usuário atual tem uma data de nascimento de declaração que foi emitido por um emissor conhecido e confiável. A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída será retornada. Quando uma declaração estiver presente, a idade do usuário é calculada. Se o usuário atender a idade mínima definida pelo requisito, a autorização seja considerada bem-sucedida. Quando a autorização for bem-sucedida, `context.Succeed` é invocado com o requisito satisfeito como seu único parâmetro.

### <a name="use-a-handler-for-multiple-requirements"></a>Usar um manipulador para várias necessidades

Este é um exemplo de uma relação um-para-muitos em que um manipulador de permissão pode lidar com três tipos diferentes de requisitos:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém requisitos não marcada como bem-sucedido. Para um `ReadPermission` requisito, o usuário deve ser um proprietário ou um responsável para acessar o recurso solicitado. No caso de um `EditPermission` ou `DeletePermission` requisito, ele ou ela deve ser um proprietário para acessar o recurso solicitado.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registro do manipulador

Manipuladores são registrados na coleção de serviços durante a configuração. Por exemplo:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63-65,72)]

O código precedente registra `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Manipuladores podem ser registrados usando qualquer um dos internos [tempos de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>O que deve retornar um manipulador?

Observe que o `Handle` método na [exemplo de manipulador](#security-authorization-handler-example) não retorna nenhum valor. Como é um status de êxito ou falha indicado?

* Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)`, passando o requisito de que foi validada com êxito.

* Um manipulador não precisa lidar com falhas em geral, como outros manipuladores para o mesmo requisito podem ter êxito.

* Para garantir a falha, mesmo se outros manipuladores de requisito tenha êxito, chame `context.Fail`.

Quando definido como `false`, o [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) propriedade (disponível no ASP.NET Core 1.1 e posterior) causam curto-circuito a execução de manipuladores quando `context.Fail` é chamado. `InvokeHandlersAfterFailure` o padrão é `true`, caso em que todos os manipuladores são chamados. Isso permite que os requisitos produzir efeitos colaterais, como registro em log, que sempre ocorrem, mesmo se `context.Fail` foi chamado no manipulador de outro.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Por que eu desejaria vários manipuladores para um requisito?

Em casos onde você deseja avaliação esteja em um **ou** base, implementar vários manipuladores para um requisito. Por exemplo, a Microsoft tem portas que apenas abrir com cartões de chave. Se você deixar o seu cartão de chave em casa, o recepcionista imprime um adesivo temporário e abre as portas para você. Nesse cenário, você teria um requisito *BuildingEntry*, mas vários manipuladores, cada um deles examinando um requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Certifique-se de que ambos os manipuladores são [registrado](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Se o manipulador é bem-sucedida quando uma política avalia o `BuildingEntryRequirement`, a avaliação da política é bem-sucedida.

## <a name="using-a-func-to-fulfill-a-policy"></a>Usando um func para atender a uma política

Pode haver situações nas quais cumprir uma política é simple de expressar em código. É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar a política com o `RequireAssertion` criador de políticas.

Por exemplo, o anterior `BadgeEntryHandler` poderia ser reescrito da seguinte maneira:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=52-53,57-63)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Acessando o contexto de solicitação do MVC nos manipuladores

O `HandleRequirementAsync` implementar em um manipulador de autorização de método tem dois parâmetros: uma `AuthorizationHandlerContext` e o `TRequirement` manipulada. Estruturas como MVC ou Jabbr serão livres para adicionar qualquer objeto para o `Resource` propriedade no `AuthorizationHandlerContext` para passar informações adicionais.

Por exemplo, o MVC passa uma instância do [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) no `Resource` propriedade. Esta propriedade fornece acesso aos `HttpContext`, `RouteData`e tudo o que outro fornecidos pelo MVC e páginas do Razor.

O uso do `Resource` é de propriedade específica do framework. Usando as informações no `Resource` propriedade limita suas políticas de autorização para determinadas estruturas. Você deve converter o `Resource` propriedade usando o `is` palavra-chave e, em seguida, confirme se a conversão foi bem-sucedida para garantir que seu código não falha com um `InvalidCastException` quando executado em outras estruturas:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
