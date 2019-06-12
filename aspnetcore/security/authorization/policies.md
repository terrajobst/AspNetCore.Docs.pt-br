---
title: Autorização baseada em política no ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar manipuladores de política de autorização para impor requisitos de autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: 67337c847ba71df3fe61250996ec944632ad5d57
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837350"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="959cd-103">Autorização baseada em política no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="959cd-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="959cd-104">Nos bastidores, [autorização baseada em funções](xref:security/authorization/roles) e [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisito e uma política previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="959cd-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="959cd-105">Esses blocos de construção dão suporte a expressão de avaliações de autorização no código.</span><span class="sxs-lookup"><span data-stu-id="959cd-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="959cd-106">O resultado é uma estrutura de autorização mais sofisticados, reutilizáveis e testáveis.</span><span class="sxs-lookup"><span data-stu-id="959cd-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="959cd-107">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="959cd-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="959cd-108">Ele é registrado como parte da configuração do serviço de autorização no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="959cd-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="959cd-109">No exemplo anterior, uma política de "AtLeast21" é criada.</span><span class="sxs-lookup"><span data-stu-id="959cd-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="959cd-110">Ele tem um requisito&mdash;da idade mínima, que é fornecida como um parâmetro para o requisito.</span><span class="sxs-lookup"><span data-stu-id="959cd-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="959cd-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="959cd-111">IAuthorizationService</span></span> 

<span data-ttu-id="959cd-112">O serviço primário que determina se a autorização for bem-sucedida é <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="959cd-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="959cd-113">O código anterior destaca os dois métodos do [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="959cd-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="959cd-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> é um serviço de marcador com nenhum método e o mecanismo para controlar se a autorização for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="959cd-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="959cd-115">Cada <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> é responsável por verificar se os requisitos sejam atendidos:</span><span class="sxs-lookup"><span data-stu-id="959cd-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="959cd-116">O <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe é o que o manipulador usa para marcar se os requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="959cd-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="959cd-117">O código a seguir mostra o simplificado (e anotado com comentários) a implementação padrão de serviço de autorização:</span><span class="sxs-lookup"><span data-stu-id="959cd-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="959cd-118">O código a seguir mostra um típico `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="959cd-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="959cd-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something"]` para autorização.</span><span class="sxs-lookup"><span data-stu-id="959cd-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something"]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="959cd-120">Aplicar políticas a controladores do MVC</span><span class="sxs-lookup"><span data-stu-id="959cd-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="959cd-121">Se você estiver usando as páginas do Razor, consulte [aplicação de políticas para as páginas do Razor](#applying-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="959cd-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="959cd-122">As políticas são aplicadas aos controladores por meio de `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="959cd-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="959cd-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="959cd-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="959cd-124">Aplicar políticas a páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="959cd-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="959cd-125">As políticas são aplicadas a páginas do Razor usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="959cd-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="959cd-126">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="959cd-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="959cd-127">Políticas também podem ser aplicadas a páginas do Razor usando um [convenção autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="959cd-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="959cd-128">Requisitos</span><span class="sxs-lookup"><span data-stu-id="959cd-128">Requirements</span></span>

<span data-ttu-id="959cd-129">Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de segurança do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="959cd-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="959cd-130">Em nossa política de "AtLeast21", o requisito é um único parâmetro&mdash;a idade mínima.</span><span class="sxs-lookup"><span data-stu-id="959cd-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="959cd-131">Implementa um requisito [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazio.</span><span class="sxs-lookup"><span data-stu-id="959cd-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="959cd-132">Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="959cd-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="959cd-133">Se uma política de autorização contém vários requisitos de autorização, devem passar todos os requisitos para que a avaliação da política seja bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="959cd-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="959cd-134">Em outras palavras, os vários requisitos de autorização adicionados a uma política de autorização única são tratados em um **AND** base.</span><span class="sxs-lookup"><span data-stu-id="959cd-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="959cd-135">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="959cd-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="959cd-136">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="959cd-136">Authorization handlers</span></span>

<span data-ttu-id="959cd-137">Um manipulador de autorização é responsável para a avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="959cd-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="959cd-138">O manipulador de autorização avalia os requisitos em relação a um fornecido [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) para determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="959cd-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="959cd-139">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="959cd-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="959cd-140">Um manipulador pode herdar [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), onde `TRequirement` é o requisito para ser tratada.</span><span class="sxs-lookup"><span data-stu-id="959cd-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="959cd-141">Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="959cd-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="959cd-142">Usar um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="959cd-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="959cd-143">Este é um exemplo de uma relação um para um em que um manipulador de idade mínima utiliza um requisito:</span><span class="sxs-lookup"><span data-stu-id="959cd-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="959cd-144">O código anterior determina se a entidade de usuário atual tem uma data de nascimento de declaração que foi emitido por um emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="959cd-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="959cd-145">A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída será retornada.</span><span class="sxs-lookup"><span data-stu-id="959cd-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="959cd-146">Quando uma declaração estiver presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="959cd-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="959cd-147">Se o usuário atender a idade mínima definida pelo requisito, a autorização seja considerada bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="959cd-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="959cd-148">Quando a autorização for bem-sucedida, `context.Succeed` é invocado com o requisito satisfeito como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="959cd-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="959cd-149">Usar um manipulador para várias necessidades</span><span class="sxs-lookup"><span data-stu-id="959cd-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="959cd-150">Este é um exemplo de uma relação um-para-muitos em que um manipulador de permissão pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="959cd-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="959cd-151">O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém requisitos não marcada como bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="959cd-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="959cd-152">Para um `ReadPermission` requisito, o usuário deve ser um proprietário ou um responsável para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="959cd-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="959cd-153">No caso de um `EditPermission` ou `DeletePermission` requisito, ele ou ela deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="959cd-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="959cd-154">Registro do manipulador</span><span class="sxs-lookup"><span data-stu-id="959cd-154">Handler registration</span></span>

<span data-ttu-id="959cd-155">Manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="959cd-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="959cd-156">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="959cd-156">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="959cd-157">O código precedente registra `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="959cd-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="959cd-158">Manipuladores podem ser registrados usando qualquer um dos internos [tempos de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="959cd-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="959cd-159">O que deve retornar um manipulador?</span><span class="sxs-lookup"><span data-stu-id="959cd-159">What should a handler return?</span></span>

<span data-ttu-id="959cd-160">Observe que o `Handle` método na [exemplo de manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="959cd-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="959cd-161">Como é um status de êxito ou falha indicado?</span><span class="sxs-lookup"><span data-stu-id="959cd-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="959cd-162">Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)`, passando o requisito de que foi validada com êxito.</span><span class="sxs-lookup"><span data-stu-id="959cd-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="959cd-163">Um manipulador não precisa lidar com falhas em geral, como outros manipuladores para o mesmo requisito podem ter êxito.</span><span class="sxs-lookup"><span data-stu-id="959cd-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="959cd-164">Para garantir a falha, mesmo se outros manipuladores de requisito tenha êxito, chame `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="959cd-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="959cd-165">Se chama um manipulador `context.Succeed` ou `context.Fail`, todos os outros manipuladores ainda são chamados.</span><span class="sxs-lookup"><span data-stu-id="959cd-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="959cd-166">Isso permite que os requisitos produzir efeitos colaterais, como registro em log, que é executado, mesmo se outro manipulador com êxito foi validado ou falha de um requisito.</span><span class="sxs-lookup"><span data-stu-id="959cd-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="959cd-167">Quando definido como `false`, o [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) propriedade (disponível no ASP.NET Core 1.1 e posterior) causam curto-circuito a execução de manipuladores quando `context.Fail` é chamado.</span><span class="sxs-lookup"><span data-stu-id="959cd-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="959cd-168">`InvokeHandlersAfterFailure` o padrão é `true`, caso em que todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="959cd-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="959cd-169">Manipuladores de autorização são chamados, mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="959cd-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="959cd-170">Por que eu desejaria vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="959cd-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="959cd-171">Em casos onde você deseja avaliação esteja em um **ou** base, implementar vários manipuladores para um requisito.</span><span class="sxs-lookup"><span data-stu-id="959cd-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="959cd-172">Por exemplo, a Microsoft tem portas que apenas abrir com cartões de chave.</span><span class="sxs-lookup"><span data-stu-id="959cd-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="959cd-173">Se você deixar o seu cartão de chave em casa, o recepcionista imprime um adesivo temporário e abre as portas para você.</span><span class="sxs-lookup"><span data-stu-id="959cd-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="959cd-174">Nesse cenário, você teria um requisito *BuildingEntry*, mas vários manipuladores, cada um deles examinando um requisito.</span><span class="sxs-lookup"><span data-stu-id="959cd-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="959cd-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="959cd-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="959cd-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="959cd-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="959cd-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="959cd-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="959cd-178">Certifique-se de que ambos os manipuladores são [registrado](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="959cd-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="959cd-179">Se o manipulador é bem-sucedida quando uma política avalia o `BuildingEntryRequirement`, a avaliação da política é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="959cd-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="959cd-180">Usando um func para atender a uma política</span><span class="sxs-lookup"><span data-stu-id="959cd-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="959cd-181">Pode haver situações nas quais cumprir uma política é simple de expressar em código.</span><span class="sxs-lookup"><span data-stu-id="959cd-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="959cd-182">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar a política com o `RequireAssertion` criador de políticas.</span><span class="sxs-lookup"><span data-stu-id="959cd-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="959cd-183">Por exemplo, o anterior `BadgeEntryHandler` poderia ser reescrito da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="959cd-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="959cd-184">Acessando o contexto de solicitação do MVC nos manipuladores</span><span class="sxs-lookup"><span data-stu-id="959cd-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="959cd-185">O `HandleRequirementAsync` implementar em um manipulador de autorização de método tem dois parâmetros: uma `AuthorizationHandlerContext` e o `TRequirement` manipulada.</span><span class="sxs-lookup"><span data-stu-id="959cd-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="959cd-186">Estruturas como MVC ou Jabbr serão livres para adicionar qualquer objeto para o `Resource` propriedade no `AuthorizationHandlerContext` para passar informações adicionais.</span><span class="sxs-lookup"><span data-stu-id="959cd-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="959cd-187">Por exemplo, o MVC passa uma instância do [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) no `Resource` propriedade.</span><span class="sxs-lookup"><span data-stu-id="959cd-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="959cd-188">Esta propriedade fornece acesso aos `HttpContext`, `RouteData`e tudo o que outro fornecidos pelo MVC e páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="959cd-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="959cd-189">O uso do `Resource` é de propriedade específica do framework.</span><span class="sxs-lookup"><span data-stu-id="959cd-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="959cd-190">Usando as informações no `Resource` propriedade limita suas políticas de autorização para determinadas estruturas.</span><span class="sxs-lookup"><span data-stu-id="959cd-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="959cd-191">Você deve converter o `Resource` propriedade usando o `is` palavra-chave e, em seguida, confirme se a conversão foi bem-sucedida para garantir que seu código não falha com um `InvalidCastException` quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="959cd-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
