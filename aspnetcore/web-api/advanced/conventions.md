---
title: Usar convenções de API Web
author: pranavkm
description: Saiba mais sobre as convenções de API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 11/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: ede9a46c160cf6a49aa93da710af0bf0b8f59acc
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710069"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="5da9c-103">Usar convenções de API Web</span><span class="sxs-lookup"><span data-stu-id="5da9c-103">Use web API conventions</span></span>

<span data-ttu-id="5da9c-104">O ASP.NET Core 2.2 apresenta uma forma de extrair a [documentação da API](xref:tutorials/web-api-help-pages-using-swagger) comum e aplicá-la a várias ações, controladores ou a todos os controladores em um assembly.</span><span class="sxs-lookup"><span data-stu-id="5da9c-104">ASP.NET Core 2.2 introduces a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="5da9c-105">As convenções de API Web são um substituto para ambientar as ações individuais com [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span><span class="sxs-lookup"><span data-stu-id="5da9c-105">Web API conventions are a substitute for decorating individual actions with [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span> <span data-ttu-id="5da9c-106">Isso permite definir os tipos de retorno "convencionais" mais comuns e os códigos de status que você retorna de sua ação com uma maneira de selecionar o método de convenção que se aplica a uma ação.</span><span class="sxs-lookup"><span data-stu-id="5da9c-106">It allows you to define the most common "conventional" return types and status codes that you return from your action with a way to select the convention method that applies to an action.</span></span>

<span data-ttu-id="5da9c-107">Por padrão, o ASP.NET Core MVC 2.2 é fornecido com um conjunto de convenções padrão, `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span><span class="sxs-lookup"><span data-stu-id="5da9c-107">By default, ASP.NET Core MVC 2.2 ships with a set of default conventions, `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span></span> <span data-ttu-id="5da9c-108">As convenções baseiam-se no controlador ao qual o ASP.NET Core efetua o processo de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5da9c-108">The conventions are based on the controller that ASP.NET Core scaffolds.</span></span> <span data-ttu-id="5da9c-109">Se suas ações seguem o padrão que o scaffolding produz, você deve ter êxito ao usar as convenções padrão.</span><span class="sxs-lookup"><span data-stu-id="5da9c-109">If your actions follow the pattern that scaffolding produces, you should be successful using the default conventions.</span></span>

<span data-ttu-id="5da9c-110">No tempo de execução, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> reconhece as convenções.</span><span class="sxs-lookup"><span data-stu-id="5da9c-110">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="5da9c-111">`ApiExplorer` é a abstração do MVC para comunicação com geradores de documento da API aberta.</span><span class="sxs-lookup"><span data-stu-id="5da9c-111">`ApiExplorer` is MVC's abstraction to communicate with Open API document generators.</span></span> <span data-ttu-id="5da9c-112">Os atributos da convenção aplicada são associados a uma ação e estão incluídos na documentação do Swagger da ação.</span><span class="sxs-lookup"><span data-stu-id="5da9c-112">Attributes from the applied convention get associated with an action and are included in the action's Swagger documentation.</span></span> <span data-ttu-id="5da9c-113">Analisadores de API também reconhecem as convenções.</span><span class="sxs-lookup"><span data-stu-id="5da9c-113">API analyzers also understand conventions.</span></span> <span data-ttu-id="5da9c-114">Se a ação for não convencional (por exemplo, ela retorna um código de status que não está documentado pela convenção aplicada), ela produzirá um aviso, incentivando você a fazer a documentação.</span><span class="sxs-lookup"><span data-stu-id="5da9c-114">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), it produces a warning, encouraging you to document it.</span></span>

<span data-ttu-id="5da9c-115">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5da9c-115">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="5da9c-116">Aplicar convenções de API Web</span><span class="sxs-lookup"><span data-stu-id="5da9c-116">Apply web API conventions</span></span>

<span data-ttu-id="5da9c-117">Há três maneiras de aplicar uma convenção.</span><span class="sxs-lookup"><span data-stu-id="5da9c-117">There are three ways to apply a convention.</span></span> <span data-ttu-id="5da9c-118">As convenções não fazem composição.</span><span class="sxs-lookup"><span data-stu-id="5da9c-118">Conventions don't compose.</span></span> <span data-ttu-id="5da9c-119">Cada ação pode ser associada a exatamente uma convenção.</span><span class="sxs-lookup"><span data-stu-id="5da9c-119">Each action may be associated with exactly one convention.</span></span> <span data-ttu-id="5da9c-120">Convenções mais específicas (detalhadas abaixo) têm precedência sobre as menos específicas.</span><span class="sxs-lookup"><span data-stu-id="5da9c-120">More specific conventions (detailed below) take precedence over less specific ones.</span></span> <span data-ttu-id="5da9c-121">A seleção é não determinística quando duas ou mais convenções da mesma prioridade se aplicam a uma ação.</span><span class="sxs-lookup"><span data-stu-id="5da9c-121">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="5da9c-122">As seguintes opções existem para aplicar uma convenção a uma ação, da mais específica à menos específica:</span><span class="sxs-lookup"><span data-stu-id="5da9c-122">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="5da9c-123">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; aplica-se a ações individuais e especifica o tipo de convenção e o método de convenção que se aplica.</span><span class="sxs-lookup"><span data-stu-id="5da9c-123">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span> <span data-ttu-id="5da9c-124">No exemplo a seguir, o método de convenção `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` é aplicado à ação `Update`:</span><span class="sxs-lookup"><span data-stu-id="5da9c-124">In the following sample, the convention method `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventionmethod&highlight=2-3)]

1. <span data-ttu-id="5da9c-125">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um controlador &mdash; Aplica-se o tipo de convenção a todas as ações no controlador.</span><span class="sxs-lookup"><span data-stu-id="5da9c-125">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the convention type to all actions on the controller.</span></span> <span data-ttu-id="5da9c-126">Os métodos de convenção apresentam dicas que determinam quais ações seriam aplicáveis (detalhes como parte da criação de convenções).</span><span class="sxs-lookup"><span data-stu-id="5da9c-126">Convention methods are decorated with hints that determine which actions it would apply to (details as part of authoring conventions).</span></span> <span data-ttu-id="5da9c-127">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5da9c-127">For example:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventiontypeattribute)]

1. <span data-ttu-id="5da9c-128">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um assembly &mdash; Aplica-se o tipo de convenção a todos os controladores no assembly atual.</span><span class="sxs-lookup"><span data-stu-id="5da9c-128">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="5da9c-129">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5da9c-129">For example:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=apiconventiontypeattribute)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="5da9c-130">Criar convenções de API Web</span><span class="sxs-lookup"><span data-stu-id="5da9c-130">Create web API conventions</span></span>

<span data-ttu-id="5da9c-131">Uma convenção é um tipo estático com métodos.</span><span class="sxs-lookup"><span data-stu-id="5da9c-131">A convention is a static type with methods.</span></span> <span data-ttu-id="5da9c-132">Esses métodos são anotados com atributos `[ProducesResponseType]` ou `[ProducesDefaultResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="5da9c-132">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="5da9c-133">Aplicar essa convenção a um assembly resulta no método de convenção sendo aplicado a qualquer ação com o nome `Find` e tendo exatamente um parâmetro chamado `id`, desde que eles não tenham outros atributos de metadados mais específicos.</span><span class="sxs-lookup"><span data-stu-id="5da9c-133">Applying this convention to an assembly results in the convention method applying to any action with the name `Find` and having exactly one parameter named `id`, as long as they don't have other more specific metadata attributes.</span></span>

<span data-ttu-id="5da9c-134">Além de `[ProducesResponseType]` e `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` e `[ApiConventionTypeMatch]` podem ser aplicados ao método de convenção que determina os métodos aos quais eles são aplicáveis.</span><span class="sxs-lookup"><span data-stu-id="5da9c-134">In addition to `[ProducesResponseType]` and `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` can be applied to the convention method that determines the methods they apply to.</span></span> <span data-ttu-id="5da9c-135">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5da9c-135">For example:</span></span>

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

* <span data-ttu-id="5da9c-136">A opção `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` aplicada ao método, indica que a convenção pode corresponder a qualquer ação desde que seja prefixada com "Find".</span><span class="sxs-lookup"><span data-stu-id="5da9c-136">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method, indicates that the convention can match any action as long as it's prefixed with "Find".</span></span> <span data-ttu-id="5da9c-137">Isso inclui métodos como `Find`, `FindPet` ou `FindById`.</span><span class="sxs-lookup"><span data-stu-id="5da9c-137">This includes methods such as `Find`, `FindPet`, or `FindById`.</span></span>
* <span data-ttu-id="5da9c-138">O `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` aplicado ao parâmetro indica que a convenção pode corresponder a métodos com exatamente um parâmetro encerrando no identificador do sufixo.</span><span class="sxs-lookup"><span data-stu-id="5da9c-138">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention can match methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="5da9c-139">Isso inclui parâmetros como `id` ou `petId`.</span><span class="sxs-lookup"><span data-stu-id="5da9c-139">This includes parameters such as `id` or `petId`.</span></span> <span data-ttu-id="5da9c-140">`ApiConventionTypeMatch` pode ser aplicado da mesma forma aos tipos para restringir o tipo do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="5da9c-140">`ApiConventionTypeMatch` can be similarly applied to types to constrain the type of the parameter.</span></span> <span data-ttu-id="5da9c-141">Um argumento `params[]` pode ser usado para indicar parâmetros restantes que não precisam ser explicitamente correspondidos.</span><span class="sxs-lookup"><span data-stu-id="5da9c-141">A `params[]` argument can be used to indicate remaining parameters that don't need to be explicitly matched.</span></span>
