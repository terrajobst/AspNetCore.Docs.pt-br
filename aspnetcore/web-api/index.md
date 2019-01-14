---
title: Criar APIs Web com o ASP.NET Core
author: scottaddie
description: Saiba mais sobre os recursos disponíveis para a criação de uma API Web no ASP.NET Core e quando é apropriado usar cada recurso.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/11/2019
uid: web-api/index
ms.openlocfilehash: a826bdecdd3a25eb23597123166695c169ba4229
ms.sourcegitcommit: ec71fd5a988f927ae301813aae5ff764feb3bb6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249432"
---
# <a name="build-web-apis-with-aspnet-core"></a><span data-ttu-id="98ea6-103">Criar APIs Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98ea6-103">Build web APIs with ASP.NET Core</span></span>

<span data-ttu-id="98ea6-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="98ea6-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="98ea6-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="98ea6-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="98ea6-106">Este documento explica como criar uma API Web no ASP.NET Core e quando é mais adequado usar cada recurso.</span><span class="sxs-lookup"><span data-stu-id="98ea6-106">This document explains how to build a web API in ASP.NET Core and when it's most appropriate to use each feature.</span></span>

## <a name="derive-class-from-controllerbase"></a><span data-ttu-id="98ea6-107">Derivar a classe por meio do ControllerBase</span><span class="sxs-lookup"><span data-stu-id="98ea6-107">Derive class from ControllerBase</span></span>

<span data-ttu-id="98ea6-108">Herde da classe <xref:Microsoft.AspNetCore.Mvc.ControllerBase> em um controlador destinado a funcionar como uma API Web.</span><span class="sxs-lookup"><span data-stu-id="98ea6-108">Inherit from the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class in a controller that's intended to serve as a web API.</span></span> <span data-ttu-id="98ea6-109">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="98ea6-109">For example:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

<span data-ttu-id="98ea6-110">A classe `ControllerBase` fornece acesso a várias propriedades e métodos.</span><span class="sxs-lookup"><span data-stu-id="98ea6-110">The `ControllerBase` class provides access to several properties and methods.</span></span> <span data-ttu-id="98ea6-111">No código anterior, os exemplos incluem <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-111">In the preceding code, examples include <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>.</span></span> <span data-ttu-id="98ea6-112">Esses métodos são chamados em métodos de ação para retornar os códigos de status HTTP 400 e 201, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="98ea6-112">These methods are called within action methods to return HTTP 400 and 201 status codes, respectively.</span></span> <span data-ttu-id="98ea6-113">A propriedade <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState>, também fornecida pelo `ControllerBase`, é acessada para executar a validação do modelo de solicitação.</span><span class="sxs-lookup"><span data-stu-id="98ea6-113">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState> property, also provided by `ControllerBase`, is accessed to handle request model validation.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="annotation-with-apicontroller-attribute"></a><span data-ttu-id="98ea6-114">Anotação com o atributo ApiController</span><span class="sxs-lookup"><span data-stu-id="98ea6-114">Annotation with ApiController attribute</span></span>

<span data-ttu-id="98ea6-115">O ASP.NET Core 2.1 apresenta o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) para denotar uma classe do controlador API Web.</span><span class="sxs-lookup"><span data-stu-id="98ea6-115">ASP.NET Core 2.1 introduces the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute to denote a web API controller class.</span></span> <span data-ttu-id="98ea6-116">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="98ea6-116">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="98ea6-117">Uma versão de compatibilidade do 2.1 ou posterior, definida por meio de <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, é necessária para usar esse atributo no nível do controlador.</span><span class="sxs-lookup"><span data-stu-id="98ea6-117">A compatibility version of 2.1 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the controller level.</span></span> <span data-ttu-id="98ea6-118">Por exemplo, o código realçado em `Startup.ConfigureServices` define o sinalizador de compatibilidade 2.1:</span><span class="sxs-lookup"><span data-stu-id="98ea6-118">For example, the highlighted code in `Startup.ConfigureServices` sets the 2.1 compatibility flag:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=2)]

<span data-ttu-id="98ea6-119">Para obter mais informações, consulte <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-119">For more information, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="98ea6-120">No ASP.NET Core 2.2 ou posterior, o atributo `[ApiController]` pode ser aplicado a um assembly.</span><span class="sxs-lookup"><span data-stu-id="98ea6-120">In ASP.NET Core 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="98ea6-121">A anotação dessa maneira aplica o comportamento da API Web para todos os controladores no assembly.</span><span class="sxs-lookup"><span data-stu-id="98ea6-121">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="98ea6-122">Lembre-se de que não é possível recusar controladores individuais.</span><span class="sxs-lookup"><span data-stu-id="98ea6-122">Beware that there's no way to opt out for individual controllers.</span></span> <span data-ttu-id="98ea6-123">Como uma recomendação, os atributos de nível de assembly devem ser aplicados à classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-123">As a recommendation, assembly-level attributes should be applied to the `Startup` class:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ApiControllerAttributeOnAssembly&highlight=1)]

<span data-ttu-id="98ea6-124">Uma versão de compatibilidade do 2.2 ou posterior, definida por meio de <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, é necessária para usar esse atributo no nível do assembly.</span><span class="sxs-lookup"><span data-stu-id="98ea6-124">A compatibility version of 2.2 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the assembly level.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="98ea6-125">Geralmente, o atributo `[ApiController]` é associado ao `ControllerBase` para habilitar o comportamento específico de REST para controladores.</span><span class="sxs-lookup"><span data-stu-id="98ea6-125">The `[ApiController]` attribute is commonly coupled with `ControllerBase` to enable REST-specific behavior for controllers.</span></span> <span data-ttu-id="98ea6-126">`ControllerBase` fornece acesso a métodos como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-126">`ControllerBase` provides access to methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*>.</span></span>

<span data-ttu-id="98ea6-127">Outra abordagem é criar uma classe de base de controlador personalizada anotada com o atributo `[ApiController]`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-127">Another approach is to create a custom base controller class annotated with the `[ApiController]` attribute:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]

<span data-ttu-id="98ea6-128">As seções a seguir descrevem os recursos de conveniência adicionados pelo atributo.</span><span class="sxs-lookup"><span data-stu-id="98ea6-128">The following sections describe convenience features added by the attribute.</span></span>

### <a name="automatic-http-400-responses"></a><span data-ttu-id="98ea6-129">Respostas automáticas do HTTP 400</span><span class="sxs-lookup"><span data-stu-id="98ea6-129">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="98ea6-130">Os erros de validação do modelo disparam uma resposta HTTP 400 automaticamente.</span><span class="sxs-lookup"><span data-stu-id="98ea6-130">Model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="98ea6-131">Consequentemente, o código a seguir se torna desnecessário em suas ações:</span><span class="sxs-lookup"><span data-stu-id="98ea6-131">Consequently, the following code becomes unnecessary in your actions:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_ModelStateIsValidCheck)]

<span data-ttu-id="98ea6-132">Use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> para personalizar a saída da resposta resultante.</span><span class="sxs-lookup"><span data-stu-id="98ea6-132">Use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to customize the output of the resulting response.</span></span>

<span data-ttu-id="98ea6-133">A desabilitação do comportamento padrão é útil quando a ação pode se recuperar de um erro de validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="98ea6-133">Disabling the default behavior is useful when your action can recover from a model validation error.</span></span> <span data-ttu-id="98ea6-134">O comportamento padrão é desabilitado quando a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> está definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-134">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property is set to `true`.</span></span> <span data-ttu-id="98ea6-135">Adicione o seguinte código em `Startup.ConfigureServices` após `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-135">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="98ea6-136">Com um sinalizador de compatibilidade do 2.2 ou posterior, o tipo de resposta padrão para respostas HTTP 400 é <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-136">With a compatibility flag of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="98ea6-137">O tipo `ValidationProblemDetails` está em conformidade com a [especificação RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="98ea6-137">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span> <span data-ttu-id="98ea6-138">Defina a propriedade `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` como `true` para retornar o formato de erro do ASP.NET Core 2.1 de <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-138">Set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` to instead return the ASP.NET Core 2.1 error format of <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="98ea6-139">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-139">Add the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
    .ConfigureApiBehaviorOptions(options =>
    {
        options
          .SuppressUseValidationProblemDetailsForInvalidModelStateResponses = true;
    });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="binding-source-parameter-inference"></a><span data-ttu-id="98ea6-140">Inferência de parâmetro de origem de associação</span><span class="sxs-lookup"><span data-stu-id="98ea6-140">Binding source parameter inference</span></span>

<span data-ttu-id="98ea6-141">Um atributo de origem de associação define o local no qual o valor do parâmetro de uma ação é encontrado.</span><span class="sxs-lookup"><span data-stu-id="98ea6-141">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="98ea6-142">Os seguintes atributos da origem da associação existem:</span><span class="sxs-lookup"><span data-stu-id="98ea6-142">The following binding source attributes exist:</span></span>

|<span data-ttu-id="98ea6-143">Atributo</span><span class="sxs-lookup"><span data-stu-id="98ea6-143">Attribute</span></span>|<span data-ttu-id="98ea6-144">Fonte de associação</span><span class="sxs-lookup"><span data-stu-id="98ea6-144">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="98ea6-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span><span class="sxs-lookup"><span data-stu-id="98ea6-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span></span>     | <span data-ttu-id="98ea6-146">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="98ea6-146">Request body</span></span> |
|<span data-ttu-id="98ea6-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span><span class="sxs-lookup"><span data-stu-id="98ea6-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span></span>     | <span data-ttu-id="98ea6-148">Dados do formulário no corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="98ea6-148">Form data in the request body</span></span> |
|<span data-ttu-id="98ea6-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span><span class="sxs-lookup"><span data-stu-id="98ea6-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span></span> | <span data-ttu-id="98ea6-150">Cabeçalho da solicitação</span><span class="sxs-lookup"><span data-stu-id="98ea6-150">Request header</span></span> |
|<span data-ttu-id="98ea6-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span><span class="sxs-lookup"><span data-stu-id="98ea6-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span></span>   | <span data-ttu-id="98ea6-152">Parâmetro de cadeia de caracteres de consulta de solicitação</span><span class="sxs-lookup"><span data-stu-id="98ea6-152">Request query string parameter</span></span> |
|<span data-ttu-id="98ea6-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span><span class="sxs-lookup"><span data-stu-id="98ea6-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span></span>   | <span data-ttu-id="98ea6-154">Dados de rota da solicitação atual</span><span class="sxs-lookup"><span data-stu-id="98ea6-154">Route data from the current request</span></span> |
|<span data-ttu-id="98ea6-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span><span class="sxs-lookup"><span data-stu-id="98ea6-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span></span> | <span data-ttu-id="98ea6-156">O serviço de solicitação inserido como um parâmetro de ação</span><span class="sxs-lookup"><span data-stu-id="98ea6-156">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="98ea6-157">Não use `[FromRoute]` quando os valores puderem conter `%2f` (ou seja, `/`).</span><span class="sxs-lookup"><span data-stu-id="98ea6-157">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="98ea6-158">`%2f` não ficará sem escape para `/`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-158">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="98ea6-159">Use `[FromQuery]`, se o valor puder conter `%2f`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-159">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="98ea6-160">Sem o atributo `[ApiController]`, os atributos da origem da associação são definidos explicitamente.</span><span class="sxs-lookup"><span data-stu-id="98ea6-160">Without the `[ApiController]` attribute, binding source attributes are explicitly defined.</span></span> <span data-ttu-id="98ea6-161">No exemplo a seguir, o atributo `[FromQuery]` indica que o valor do parâmetro `discontinuedOnly` é fornecido na cadeia de caracteres de consulta da URL de solicitação:</span><span class="sxs-lookup"><span data-stu-id="98ea6-161">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="98ea6-162">Regras de inferência são aplicadas para as fontes de dados padrão dos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="98ea6-162">Inference rules are applied for the default data sources of action parameters.</span></span> <span data-ttu-id="98ea6-163">Essas regras configuram as origens da associação que você provavelmente aplicaria manualmente aos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="98ea6-163">These rules configure the binding sources you're otherwise likely to manually apply to the action parameters.</span></span> <span data-ttu-id="98ea6-164">Os atributos da origem da associação se comportam da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="98ea6-164">The binding source attributes behave as follows:</span></span>

* <span data-ttu-id="98ea6-165">\*\*[FromBody] \*\* é inferido para parâmetros de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="98ea6-165">**[FromBody]** is inferred for complex type parameters.</span></span> <span data-ttu-id="98ea6-166">Uma exceção a essa regra é qualquer tipo interno complexo com um significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> e <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-166">An exception to this rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="98ea6-167">O código de inferência da origem da associação ignora esses tipos especiais.</span><span class="sxs-lookup"><span data-stu-id="98ea6-167">The binding source inference code ignores those special types.</span></span> <span data-ttu-id="98ea6-168">`[FromBody]` não é inferido para tipos simples, como `string` ou `int`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-168">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="98ea6-169">Portanto, o atributo `[FromBody]` deve ser usado para tipos simples quando essa funcionalidade for necessária.</span><span class="sxs-lookup"><span data-stu-id="98ea6-169">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span> <span data-ttu-id="98ea6-170">Quando uma ação tiver mais de um parâmetro especificado explicitamente (via `[FromBody]`) ou inferido como limite do corpo da solicitação, uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="98ea6-170">When an action has more than one parameter explicitly specified (via `[FromBody]`) or inferred as bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="98ea6-171">Por exemplo, as seguintes assinaturas de ação causam uma exceção:</span><span class="sxs-lookup"><span data-stu-id="98ea6-171">For example, the following action signatures cause an exception:</span></span>

    [!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]

    > [!NOTE]
    > <span data-ttu-id="98ea6-172">No ASP.NET Core 2.1, os parâmetros de tipo de coleção, como listas e matrizes, são inferidos incorretamente como [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute).</span><span class="sxs-lookup"><span data-stu-id="98ea6-172">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute).</span></span> <span data-ttu-id="98ea6-173">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) deverá ser usado para esses parâmetros se eles forem vinculados ao corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="98ea6-173">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="98ea6-174">Esse comportamento é corrigido no ASP.NET Core 2.2 ou posterior, onde os parâmetros do tipo de coleção são inferidos para serem vinculados ao corpo por padrão.</span><span class="sxs-lookup"><span data-stu-id="98ea6-174">This behavior is fixed in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

* <span data-ttu-id="98ea6-175">**[FromForm]** é inferido para parâmetros de ação do tipo <xref:Microsoft.AspNetCore.Http.IFormFile> e <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-175">**[FromForm]** is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="98ea6-176">Ele não é inferido para qualquer tipo simples ou definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="98ea6-176">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="98ea6-177">\*\*[FromRoute] \*\* é inferido para qualquer nome de parâmetro de ação correspondente a um parâmetro no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="98ea6-177">**[FromRoute]** is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="98ea6-178">Quando mais de uma rota correspondem a um parâmetro de ação, qualquer valor de rota é considerado `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-178">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="98ea6-179">\*\*[FromQuery] \*\* é inferido para todos os outros parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="98ea6-179">**[FromQuery]** is inferred for any other action parameters.</span></span>

<span data-ttu-id="98ea6-180">As regras de inferência padrão são desabilitadas quando a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> está definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-180">The default inference rules are disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> property is set to `true`.</span></span> <span data-ttu-id="98ea6-181">Adicione o seguinte código em `Startup.ConfigureServices` após `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-181">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="multipartform-data-request-inference"></a><span data-ttu-id="98ea6-182">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="98ea6-182">Multipart/form-data request inference</span></span>

<span data-ttu-id="98ea6-183">Quando um parâmetro de ação é anotado com o atributo [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute), o tipo de conteúdo de solicitação `multipart/form-data` é inferido.</span><span class="sxs-lookup"><span data-stu-id="98ea6-183">When an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute, the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="98ea6-184">O comportamento padrão é desabilitado quando a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> está definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-184">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property is set to `true`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="98ea6-185">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-185">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="98ea6-186">Adicione o seguinte código em `Startup.ConfigureServices` após `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-186">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="attribute-routing-requirement"></a><span data-ttu-id="98ea6-187">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="98ea6-187">Attribute routing requirement</span></span>

<span data-ttu-id="98ea6-188">O roteamento de atributo se torna um requisito.</span><span class="sxs-lookup"><span data-stu-id="98ea6-188">Attribute routing becomes a requirement.</span></span> <span data-ttu-id="98ea6-189">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="98ea6-189">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="98ea6-190">As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas em <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> ou por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-190">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined in <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="problem-details-responses-for-error-status-codes"></a><span data-ttu-id="98ea6-191">Respostas de detalhes de problema para códigos de status de erro</span><span class="sxs-lookup"><span data-stu-id="98ea6-191">Problem details responses for error status codes</span></span>

<span data-ttu-id="98ea6-192">No ASP.NET Core 2.2 ou posterior, o MVC transforma um resultado de erro (um resultado com o código de status 400 ou superior) em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="98ea6-192">In ASP.NET Core 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="98ea6-193">`ProblemDetails` é:</span><span class="sxs-lookup"><span data-stu-id="98ea6-193">`ProblemDetails` is:</span></span>

* <span data-ttu-id="98ea6-194">Um tipo baseado na [especificação RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="98ea6-194">A type based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>
* <span data-ttu-id="98ea6-195">Um formato padronizado para especificar detalhes do erro legível por computador em uma resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="98ea6-195">A standardized format for specifying machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="98ea6-196">Considere o seguinte código em uma ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="98ea6-196">Consider the following code in a controller action:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Controllers/ProductsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="98ea6-197">A resposta HTTP para `NotFound` tem um código de status 404 com um corpo `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-197">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="98ea6-198">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="98ea6-198">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

<span data-ttu-id="98ea6-199">O recurso de detalhes do problema requer um sinalizador de compatibilidade de 2.2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="98ea6-199">The problem details feature requires a compatibility flag of 2.2 or later.</span></span> <span data-ttu-id="98ea6-200">O comportamento padrão é desabilitado quando a propriedade `SuppressMapClientErrors` está definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-200">The default behavior is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="98ea6-201">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="98ea6-201">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8)]

<span data-ttu-id="98ea6-202">Use a propriedade `ClientErrorMapping` para configurar o conteúdo da resposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="98ea6-202">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="98ea6-203">Por exemplo, o código a seguir atualiza a propriedade `type` para respostas 404:</span><span class="sxs-lookup"><span data-stu-id="98ea6-203">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="98ea6-204">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="98ea6-204">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
