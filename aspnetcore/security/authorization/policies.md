---
title: Autorização baseada em política no ASP.NET Core
author: rick-anderson
description: Saiba como criar e usar manipuladores de política de autorização para impor requisitos de autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: ea9d687d3810c104d5b3fa39033849c21569709b
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068164"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="f2b23-103">Autorização baseada em política no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2b23-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="f2b23-104">Nos bastidores, [autorização baseada em funções](xref:security/authorization/roles) e [autorização baseada em declarações](xref:security/authorization/claims) usam um requisito, um manipulador de requisito e uma política previamente configurada.</span><span class="sxs-lookup"><span data-stu-id="f2b23-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="f2b23-105">Esses blocos de construção dão suporte a expressão de avaliações de autorização no código.</span><span class="sxs-lookup"><span data-stu-id="f2b23-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="f2b23-106">O resultado é uma estrutura de autorização mais sofisticados, reutilizáveis e testáveis.</span><span class="sxs-lookup"><span data-stu-id="f2b23-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="f2b23-107">Uma política de autorização consiste em um ou mais requisitos.</span><span class="sxs-lookup"><span data-stu-id="f2b23-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="f2b23-108">Ele é registrado como parte da configuração do serviço de autorização no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="f2b23-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="f2b23-109">No exemplo anterior, uma política de "AtLeast21" é criada.</span><span class="sxs-lookup"><span data-stu-id="f2b23-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="f2b23-110">Ele tem um requisito&mdash;da idade mínima, que é fornecida como um parâmetro para o requisito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="f2b23-111">Aplicar políticas a controladores do MVC</span><span class="sxs-lookup"><span data-stu-id="f2b23-111">Applying policies to MVC controllers</span></span>

<span data-ttu-id="f2b23-112">Se você estiver usando as páginas do Razor, consulte [aplicação de políticas para as páginas do Razor](#applying-policies-to-razor-pages) neste documento.</span><span class="sxs-lookup"><span data-stu-id="f2b23-112">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="f2b23-113">As políticas são aplicadas aos controladores por meio de `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="f2b23-113">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="f2b23-114">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f2b23-114">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="f2b23-115">Aplicar políticas a páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="f2b23-115">Applying policies to Razor Pages</span></span>

<span data-ttu-id="f2b23-116">As políticas são aplicadas a páginas do Razor usando o `[Authorize]` atributo com o nome da política.</span><span class="sxs-lookup"><span data-stu-id="f2b23-116">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="f2b23-117">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f2b23-117">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="f2b23-118">Políticas também podem ser aplicadas a páginas do Razor usando um [convenção autorização](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="f2b23-118">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="f2b23-119">Requisitos</span><span class="sxs-lookup"><span data-stu-id="f2b23-119">Requirements</span></span>

<span data-ttu-id="f2b23-120">Um requisito de autorização é uma coleção de parâmetros de dados que uma política pode usar para avaliar a entidade de segurança do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="f2b23-120">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="f2b23-121">Em nossa política de "AtLeast21", o requisito é um único parâmetro&mdash;a idade mínima.</span><span class="sxs-lookup"><span data-stu-id="f2b23-121">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="f2b23-122">Implementa um requisito [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), que é uma interface de marcador vazio.</span><span class="sxs-lookup"><span data-stu-id="f2b23-122">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="f2b23-123">Um requisito de idade mínima parametrizada poderia ser implementado da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="f2b23-123">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="f2b23-124">Se uma política de autorização contém vários requisitos de autorização, devem passar todos os requisitos para que a avaliação da política seja bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="f2b23-124">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="f2b23-125">Em outras palavras, os vários requisitos de autorização adicionados a uma política de autorização única são tratados em um **AND** base.</span><span class="sxs-lookup"><span data-stu-id="f2b23-125">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="f2b23-126">Um requisito não precisa ter dados ou propriedades.</span><span class="sxs-lookup"><span data-stu-id="f2b23-126">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="f2b23-127">Manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="f2b23-127">Authorization handlers</span></span>

<span data-ttu-id="f2b23-128">Um manipulador de autorização é responsável para a avaliação das propriedades de um requisito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-128">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="f2b23-129">O manipulador de autorização avalia os requisitos em relação a um fornecido [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) para determinar se o acesso é permitido.</span><span class="sxs-lookup"><span data-stu-id="f2b23-129">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="f2b23-130">Um requisito pode ter [vários manipuladores](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="f2b23-130">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="f2b23-131">Um manipulador pode herdar [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), onde `TRequirement` é o requisito para ser tratada.</span><span class="sxs-lookup"><span data-stu-id="f2b23-131">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="f2b23-132">Como alternativa, um manipulador pode implementar [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) para lidar com mais de um tipo de requisito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-132">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="f2b23-133">Usar um manipulador para um requisito</span><span class="sxs-lookup"><span data-stu-id="f2b23-133">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="f2b23-134">Este é um exemplo de uma relação um para um em que um manipulador de idade mínima utiliza um requisito:</span><span class="sxs-lookup"><span data-stu-id="f2b23-134">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="f2b23-135">O código anterior determina se a entidade de usuário atual tem uma data de nascimento de declaração que foi emitido por um emissor conhecido e confiável.</span><span class="sxs-lookup"><span data-stu-id="f2b23-135">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="f2b23-136">A autorização não pode ocorrer quando a declaração está ausente, caso em que uma tarefa concluída será retornada.</span><span class="sxs-lookup"><span data-stu-id="f2b23-136">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="f2b23-137">Quando uma declaração estiver presente, a idade do usuário é calculada.</span><span class="sxs-lookup"><span data-stu-id="f2b23-137">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="f2b23-138">Se o usuário atender a idade mínima definida pelo requisito, a autorização seja considerada bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="f2b23-138">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="f2b23-139">Quando a autorização for bem-sucedida, `context.Succeed` é invocado com o requisito satisfeito como seu único parâmetro.</span><span class="sxs-lookup"><span data-stu-id="f2b23-139">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="f2b23-140">Usar um manipulador para várias necessidades</span><span class="sxs-lookup"><span data-stu-id="f2b23-140">Use a handler for multiple requirements</span></span>

<span data-ttu-id="f2b23-141">Este é um exemplo de uma relação um-para-muitos em que um manipulador de permissão pode lidar com três tipos diferentes de requisitos:</span><span class="sxs-lookup"><span data-stu-id="f2b23-141">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="f2b23-142">O código anterior percorre [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;uma propriedade que contém requisitos não marcada como bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="f2b23-142">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="f2b23-143">Para um `ReadPermission` requisito, o usuário deve ser um proprietário ou um responsável para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="f2b23-143">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="f2b23-144">No caso de um `EditPermission` ou `DeletePermission` requisito, ele ou ela deve ser um proprietário para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="f2b23-144">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="f2b23-145">Registro do manipulador</span><span class="sxs-lookup"><span data-stu-id="f2b23-145">Handler registration</span></span>

<span data-ttu-id="f2b23-146">Manipuladores são registrados na coleção de serviços durante a configuração.</span><span class="sxs-lookup"><span data-stu-id="f2b23-146">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="f2b23-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f2b23-147">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="f2b23-148">O código precedente registra `MinimumAgeHandler` como um singleton invocando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="f2b23-148">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="f2b23-149">Manipuladores podem ser registrados usando qualquer um dos internos [tempos de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="f2b23-149">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="f2b23-150">O que deve retornar um manipulador?</span><span class="sxs-lookup"><span data-stu-id="f2b23-150">What should a handler return?</span></span>

<span data-ttu-id="f2b23-151">Observe que o `Handle` método na [exemplo de manipulador](#security-authorization-handler-example) não retorna nenhum valor.</span><span class="sxs-lookup"><span data-stu-id="f2b23-151">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="f2b23-152">Como é um status de êxito ou falha indicado?</span><span class="sxs-lookup"><span data-stu-id="f2b23-152">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="f2b23-153">Um manipulador indica êxito chamando `context.Succeed(IAuthorizationRequirement requirement)`, passando o requisito de que foi validada com êxito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-153">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="f2b23-154">Um manipulador não precisa lidar com falhas em geral, como outros manipuladores para o mesmo requisito podem ter êxito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-154">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="f2b23-155">Para garantir a falha, mesmo se outros manipuladores de requisito tenha êxito, chame `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="f2b23-155">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="f2b23-156">Se chama um manipulador `context.Succeed` ou `context.Fail`, todos os outros manipuladores ainda são chamados.</span><span class="sxs-lookup"><span data-stu-id="f2b23-156">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="f2b23-157">Isso permite que os requisitos produzir efeitos colaterais, como registro em log, que é executado, mesmo se outro manipulador com êxito foi validado ou falha de um requisito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-157">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="f2b23-158">Quando definido como `false`, o [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) propriedade (disponível no ASP.NET Core 1.1 e posterior) causam curto-circuito a execução de manipuladores quando `context.Fail` é chamado.</span><span class="sxs-lookup"><span data-stu-id="f2b23-158">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> `InvokeHandlersAfterFailure` <span data-ttu-id="f2b23-159">o padrão é `true`, caso em que todos os manipuladores são chamados.</span><span class="sxs-lookup"><span data-stu-id="f2b23-159">defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="f2b23-160">Manipuladores de autorização são chamados, mesmo se a autenticação falhar.</span><span class="sxs-lookup"><span data-stu-id="f2b23-160">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="f2b23-161">Por que eu desejaria vários manipuladores para um requisito?</span><span class="sxs-lookup"><span data-stu-id="f2b23-161">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="f2b23-162">Em casos onde você deseja avaliação esteja em um **ou** base, implementar vários manipuladores para um requisito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-162">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="f2b23-163">Por exemplo, a Microsoft tem portas que apenas abrir com cartões de chave.</span><span class="sxs-lookup"><span data-stu-id="f2b23-163">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="f2b23-164">Se você deixar o seu cartão de chave em casa, o recepcionista imprime um adesivo temporário e abre as portas para você.</span><span class="sxs-lookup"><span data-stu-id="f2b23-164">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="f2b23-165">Nesse cenário, você teria um requisito *BuildingEntry*, mas vários manipuladores, cada um deles examinando um requisito.</span><span class="sxs-lookup"><span data-stu-id="f2b23-165">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

*<span data-ttu-id="f2b23-166">BuildingEntryRequirement.cs</span><span class="sxs-lookup"><span data-stu-id="f2b23-166">BuildingEntryRequirement.cs</span></span>*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*<span data-ttu-id="f2b23-167">BadgeEntryHandler.cs</span><span class="sxs-lookup"><span data-stu-id="f2b23-167">BadgeEntryHandler.cs</span></span>*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*<span data-ttu-id="f2b23-168">TemporaryStickerHandler.cs</span><span class="sxs-lookup"><span data-stu-id="f2b23-168">TemporaryStickerHandler.cs</span></span>*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="f2b23-169">Certifique-se de que ambos os manipuladores são [registrado](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="f2b23-169">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="f2b23-170">Se o manipulador é bem-sucedida quando uma política avalia o `BuildingEntryRequirement`, a avaliação da política é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="f2b23-170">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="f2b23-171">Usando um func para atender a uma política</span><span class="sxs-lookup"><span data-stu-id="f2b23-171">Using a func to fulfill a policy</span></span>

<span data-ttu-id="f2b23-172">Pode haver situações nas quais cumprir uma política é simple de expressar em código.</span><span class="sxs-lookup"><span data-stu-id="f2b23-172">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="f2b23-173">É possível fornecer um `Func<AuthorizationHandlerContext, bool>` ao configurar a política com o `RequireAssertion` criador de políticas.</span><span class="sxs-lookup"><span data-stu-id="f2b23-173">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="f2b23-174">Por exemplo, o anterior `BadgeEntryHandler` poderia ser reescrito da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="f2b23-174">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="f2b23-175">Acessando o contexto de solicitação do MVC nos manipuladores</span><span class="sxs-lookup"><span data-stu-id="f2b23-175">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="f2b23-176">O `HandleRequirementAsync` implementar em um manipulador de autorização de método tem dois parâmetros: uma `AuthorizationHandlerContext` e o `TRequirement` manipulada.</span><span class="sxs-lookup"><span data-stu-id="f2b23-176">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="f2b23-177">Estruturas como MVC ou Jabbr serão livres para adicionar qualquer objeto para o `Resource` propriedade no `AuthorizationHandlerContext` para passar informações adicionais.</span><span class="sxs-lookup"><span data-stu-id="f2b23-177">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="f2b23-178">Por exemplo, o MVC passa uma instância do [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) no `Resource` propriedade.</span><span class="sxs-lookup"><span data-stu-id="f2b23-178">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="f2b23-179">Esta propriedade fornece acesso aos `HttpContext`, `RouteData`e tudo o que outro fornecidos pelo MVC e páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="f2b23-179">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="f2b23-180">O uso do `Resource` é de propriedade específica do framework.</span><span class="sxs-lookup"><span data-stu-id="f2b23-180">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="f2b23-181">Usando as informações no `Resource` propriedade limita suas políticas de autorização para determinadas estruturas.</span><span class="sxs-lookup"><span data-stu-id="f2b23-181">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="f2b23-182">Você deve converter o `Resource` propriedade usando o `is` palavra-chave e, em seguida, confirme se a conversão foi bem-sucedida para garantir que seu código não falha com um `InvalidCastException` quando executado em outras estruturas:</span><span class="sxs-lookup"><span data-stu-id="f2b23-182">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
