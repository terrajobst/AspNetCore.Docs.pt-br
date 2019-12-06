---
title: Usar convenções de API Web
author: pranavkm
description: Saiba mais sobre as convenções de API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: web-api/advanced/conventions
ms.openlocfilehash: 2c7e33da24322504fc5e1be83c0b814710186687
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881312"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="9af30-103">Usar convenções de API Web</span><span class="sxs-lookup"><span data-stu-id="9af30-103">Use web API conventions</span></span>

<span data-ttu-id="9af30-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm) e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="9af30-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="9af30-105">O ASP.NET Core 2.2 (e posterior) inclui uma forma de extrair a [documentação da API](xref:tutorials/web-api-help-pages-using-swagger) comum e aplicá-la a várias ações, controladores ou a todos os controladores em um assembly.</span><span class="sxs-lookup"><span data-stu-id="9af30-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="9af30-106">As convenções de API da Web são um substituto para decorar ações individuais com [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span><span class="sxs-lookup"><span data-stu-id="9af30-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="9af30-107">Uma convenção permite que você:</span><span class="sxs-lookup"><span data-stu-id="9af30-107">A convention allows you to:</span></span>

* <span data-ttu-id="9af30-108">Defina os tipos de retorno mais comuns e códigos de status retornados de um tipo de ação específico.</span><span class="sxs-lookup"><span data-stu-id="9af30-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="9af30-109">Identifica as ações que desviam do padrão definido.</span><span class="sxs-lookup"><span data-stu-id="9af30-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="9af30-110">O ASP.NET Core MVC 2.2 (e posterior) inclui um conjunto de convenções padrão em <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="9af30-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="9af30-111">As convenções são baseadas no controlador (*ValuesController.cs*) fornecido no modelo de projeto da **API** do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9af30-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="9af30-112">Se suas ações seguem o padrão no modelo, você deve ter êxito ao usar as convenções padrão.</span><span class="sxs-lookup"><span data-stu-id="9af30-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="9af30-113">Se as convenções padrão não atenderem às suas necessidades, consulte [Criar convenções de API Web](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="9af30-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="9af30-114">No runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> reconhece as convenções.</span><span class="sxs-lookup"><span data-stu-id="9af30-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="9af30-115">`ApiExplorer` é a abstração do MVC para comunicação com geradores de documento da [OpenAPI](https://www.openapis.org/) (também conhecida como Swagger).</span><span class="sxs-lookup"><span data-stu-id="9af30-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="9af30-116">Os atributos da convenção aplicada são associados a uma ação e estão incluídos na documentação da OpenAPI da ação.</span><span class="sxs-lookup"><span data-stu-id="9af30-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="9af30-117">Os [Analisadores de API](xref:web-api/advanced/analyzers) também reconhecem as convenções.</span><span class="sxs-lookup"><span data-stu-id="9af30-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="9af30-118">Se a ação for não convencional (por exemplo, ela retorna um código de status que não está documentado pela convenção aplicada), um aviso incentivará você a fazer a documentação do código de status.</span><span class="sxs-lookup"><span data-stu-id="9af30-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="9af30-119">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9af30-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="9af30-120">Aplicar convenções de API Web</span><span class="sxs-lookup"><span data-stu-id="9af30-120">Apply web API conventions</span></span>

<span data-ttu-id="9af30-121">As convenções não fazem composição. Cada ação pode ser associada a exatamente uma convenção.</span><span class="sxs-lookup"><span data-stu-id="9af30-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="9af30-122">As convenções mais específicas têm precedência sobre as menos específicas.</span><span class="sxs-lookup"><span data-stu-id="9af30-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="9af30-123">A seleção é não determinística quando duas ou mais convenções da mesma prioridade se aplicam a uma ação.</span><span class="sxs-lookup"><span data-stu-id="9af30-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="9af30-124">As seguintes opções existem para aplicar uma convenção a uma ação, da mais específica à menos específica:</span><span class="sxs-lookup"><span data-stu-id="9af30-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="9af30-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; aplica-se a ações individuais e especifica o tipo de convenção e o método de convenção que se aplica.</span><span class="sxs-lookup"><span data-stu-id="9af30-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="9af30-126">No exemplo a seguir, o método de convenção `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` do tipo de convenção padrão é aplicado à ação `Update`:</span><span class="sxs-lookup"><span data-stu-id="9af30-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="9af30-127">o método de convenção `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` aplica os seguintes atributos à ação:</span><span class="sxs-lookup"><span data-stu-id="9af30-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="9af30-128">Para saber mais sobre `[ProducesDefaultResponseType]`, confira [Resposta padrão](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="9af30-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="9af30-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um controlador &mdash;. Aplica-se o tipo de convenção especificada a todas as ações no controlador.</span><span class="sxs-lookup"><span data-stu-id="9af30-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="9af30-130">Um método de Convenção é marcado com dicas que determinam as ações às quais o método de Convenção se aplica.</span><span class="sxs-lookup"><span data-stu-id="9af30-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="9af30-131">Para obter mais informações sobre dicas, consulte [Criar convenções da API Web](#create-web-api-conventions)).</span><span class="sxs-lookup"><span data-stu-id="9af30-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="9af30-132">No exemplo a seguir, o conjunto padrão de convenções é aplicado a todas as ações no *ContactsConventionController*:</span><span class="sxs-lookup"><span data-stu-id="9af30-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="9af30-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um assembly &mdash;. Aplica-se o tipo de convenção especificada a todos os controladores no assembly atual.</span><span class="sxs-lookup"><span data-stu-id="9af30-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="9af30-134">Como recomendação, aplique atributos no nível do assembly ao arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9af30-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="9af30-135">No exemplo a seguir, o conjunto padrão de convenções é aplicado a todos os controladores no assembly:</span><span class="sxs-lookup"><span data-stu-id="9af30-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="9af30-136">Criar convenções de API Web</span><span class="sxs-lookup"><span data-stu-id="9af30-136">Create web API conventions</span></span>

<span data-ttu-id="9af30-137">Se as convenções padrão da API não atenderem às suas necessidades, crie suas próprias convenções.</span><span class="sxs-lookup"><span data-stu-id="9af30-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="9af30-138">Uma convenção é:</span><span class="sxs-lookup"><span data-stu-id="9af30-138">A convention is:</span></span>

* <span data-ttu-id="9af30-139">um tipo estático com métodos.</span><span class="sxs-lookup"><span data-stu-id="9af30-139">A static type with methods.</span></span>
* <span data-ttu-id="9af30-140">Com capacidade de definir [tipos de resposta](#response-types) e [requisitos de nomenclatura](#naming-requirements) em ações.</span><span class="sxs-lookup"><span data-stu-id="9af30-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="9af30-141">Tipos de resposta</span><span class="sxs-lookup"><span data-stu-id="9af30-141">Response types</span></span>

<span data-ttu-id="9af30-142">Esses métodos são anotados com atributos `[ProducesResponseType]` ou `[ProducesDefaultResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="9af30-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="9af30-143">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9af30-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="9af30-144">Se atributos de metadados mais específicos estão ausentes, a aplicação dessa convenção a um assembly impõe que:</span><span class="sxs-lookup"><span data-stu-id="9af30-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="9af30-145">O método de convenção se aplica a qualquer ação denominada `Find`.</span><span class="sxs-lookup"><span data-stu-id="9af30-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="9af30-146">Um parâmetro denominado `id` está presente na ação `Find`.</span><span class="sxs-lookup"><span data-stu-id="9af30-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="9af30-147">Requisitos de nomenclatura</span><span class="sxs-lookup"><span data-stu-id="9af30-147">Naming requirements</span></span>

<span data-ttu-id="9af30-148">Os atributos `[ApiConventionNameMatch]` e `[ApiConventionTypeMatch]` podem ser aplicados ao método de convenção que determina as ações às quais eles são aplicáveis.</span><span class="sxs-lookup"><span data-stu-id="9af30-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="9af30-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9af30-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="9af30-150">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9af30-150">In the preceding example:</span></span>

* <span data-ttu-id="9af30-151">A opção `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` aplicada ao método indica que a convenção corresponde a qualquer ação desde que seja prefixada com "Find".</span><span class="sxs-lookup"><span data-stu-id="9af30-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="9af30-152">Exemplos de ações correspondentes incluem `Find`, `FindPet` e `FindById`.</span><span class="sxs-lookup"><span data-stu-id="9af30-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="9af30-153">O `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` aplicado ao parâmetro indica que a convenção corresponde a métodos com exatamente um parâmetro encerrando no identificador do sufixo.</span><span class="sxs-lookup"><span data-stu-id="9af30-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="9af30-154">Exemplos incluem parâmetros como `id` ou `petId`.</span><span class="sxs-lookup"><span data-stu-id="9af30-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="9af30-155">`ApiConventionTypeMatch` pode ser aplicado da mesma forma aos tipos para restringir o tipo do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9af30-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="9af30-156">Um argumento `params[]` indica parâmetros restantes que não precisam ser explicitamente correspondidos.</span><span class="sxs-lookup"><span data-stu-id="9af30-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9af30-157">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9af30-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
