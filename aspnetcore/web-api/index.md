---
title: Criar APIs Web com o ASP.NET Core
author: scottaddie
description: Aprenda os fundamentos da criação de uma API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: 3dca07db3d6be4ab219a2e05e3adcf1b24ee5c40
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074504"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="d97f2-103">Criar APIs Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d97f2-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="d97f2-104">Por [Scott Addie](https://github.com/scottaddie) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d97f2-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d97f2-105">O ASP.NET Core permite a criação de serviços RESTful, também conhecidos como APIs Web, usando C#.</span><span class="sxs-lookup"><span data-stu-id="d97f2-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="d97f2-106">Para lidar com solicitações, uma API Web usa controladores.</span><span class="sxs-lookup"><span data-stu-id="d97f2-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="d97f2-107">Em uma API Web, os *controladores* são classes que derivam de `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="d97f2-108">Este artigo mostra como usar controladores para manipular solicitações de API da Web.</span><span class="sxs-lookup"><span data-stu-id="d97f2-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="d97f2-109">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="d97f2-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="d97f2-110">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="d97f2-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="d97f2-111">Classe ControllerBase</span><span class="sxs-lookup"><span data-stu-id="d97f2-111">ControllerBase class</span></span>

<span data-ttu-id="d97f2-112">Uma API da Web consiste em uma ou mais classes de controlador que derivam de <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="d97f2-113">O modelo de projeto de API Web fornece um controlador de início:</span><span class="sxs-lookup"><span data-stu-id="d97f2-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="d97f2-114">Não crie um controlador de API Web derivando da classe base <xref:Microsoft.AspNetCore.Mvc.Controller>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="d97f2-115">`Controller` é derivado de `ControllerBase` e agrega suporte para exibições; portanto, serve para manipulação de páginas da Web, não para solicitações de API Web.</span><span class="sxs-lookup"><span data-stu-id="d97f2-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="d97f2-116">Há uma exceção a essa regra: se você planeja usar o mesmo controlador para exibições e APIs da Web, derive-a de `Controller`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="d97f2-117">A classe `ControllerBase` fornece muitas propriedades e métodos úteis para lidar com solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d97f2-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="d97f2-118">Por exemplo, `ControllerBase.CreatedAtAction` retorna um código de status 201:</span><span class="sxs-lookup"><span data-stu-id="d97f2-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="d97f2-119">Veja mais alguns exemplos de métodos fornecidos por `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="d97f2-120">Método</span><span class="sxs-lookup"><span data-stu-id="d97f2-120">Method</span></span>   |<span data-ttu-id="d97f2-121">Observações</span><span class="sxs-lookup"><span data-stu-id="d97f2-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="d97f2-122">Retorna o código de status 400.</span><span class="sxs-lookup"><span data-stu-id="d97f2-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="d97f2-123">Retorna o código de status 404.</span><span class="sxs-lookup"><span data-stu-id="d97f2-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="d97f2-124">Retorna um arquivo.</span><span class="sxs-lookup"><span data-stu-id="d97f2-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="d97f2-125">Invoca [model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="d97f2-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="d97f2-126">Invoca [validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="d97f2-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="d97f2-127">Confira uma lista com todos os métodos e propriedades disponíveis em <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="d97f2-128">Atributos</span><span class="sxs-lookup"><span data-stu-id="d97f2-128">Attributes</span></span>

<span data-ttu-id="d97f2-129">O namespace <xref:Microsoft.AspNetCore.Mvc> fornece atributos que podem ser usados para configurar o comportamento de controladores de API Web e dos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="d97f2-130">O exemplo a seguir usa atributos para especificar o verbo de ação HTTP com suporte e quaisquer códigos de status HTTP conhecidos que poderiam ser retornados:</span><span class="sxs-lookup"><span data-stu-id="d97f2-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="d97f2-131">Confira mais alguns exemplos de atributos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d97f2-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="d97f2-132">Atributo</span><span class="sxs-lookup"><span data-stu-id="d97f2-132">Attribute</span></span>|<span data-ttu-id="d97f2-133">Observações</span><span class="sxs-lookup"><span data-stu-id="d97f2-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="d97f2-134">Especifica o padrão de URL para um controlador ou ação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="d97f2-135">Especifica o prefixo e as propriedades que serão incluídos no model binding.</span><span class="sxs-lookup"><span data-stu-id="d97f2-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="d97f2-136">Identifica uma ação que dá suporte ao verbo de ação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="d97f2-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="d97f2-137">Especifica os tipos de dados aceitos por uma ação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="d97f2-138">Especifica os tipos de dados retornados por uma ação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="d97f2-139">Veja uma lista que inclui os atributos disponíveis no namespace <xref:Microsoft.AspNetCore.Mvc>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="d97f2-140">Atributo ApiController</span><span class="sxs-lookup"><span data-stu-id="d97f2-140">ApiController attribute</span></span>

<span data-ttu-id="d97f2-141">O atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) pode ser aplicado a uma classe de controlador para habilitar os seguintes conceituada, comportamentos específicos de API:</span><span class="sxs-lookup"><span data-stu-id="d97f2-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="d97f2-142">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="d97f2-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="d97f2-143">Respostas HTTP 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="d97f2-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="d97f2-144">Inferência de parâmetro de origem da associação</span><span class="sxs-lookup"><span data-stu-id="d97f2-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="d97f2-145">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="d97f2-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="d97f2-146">Detalhes do problema dos códigos de status de erro</span><span class="sxs-lookup"><span data-stu-id="d97f2-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="d97f2-147">O recurso *detalhes do problema para códigos de status de erro* requer uma versão de [compatibilidade](xref:mvc/compatibility-version) do 2,2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="d97f2-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="d97f2-148">Os outros recursos exigem uma versão de compatibilidade do 2,1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="d97f2-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="d97f2-149">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="d97f2-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="d97f2-150">Respostas HTTP 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="d97f2-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="d97f2-151">Inferência de parâmetro de origem da associação</span><span class="sxs-lookup"><span data-stu-id="d97f2-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="d97f2-152">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="d97f2-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="d97f2-153">Esses recursos exigem [compatibilidade com a versão](xref:mvc/compatibility-version) 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="d97f2-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="d97f2-154">Atributo em controladores específicos</span><span class="sxs-lookup"><span data-stu-id="d97f2-154">Attribute on specific controllers</span></span>

<span data-ttu-id="d97f2-155">O atributo `[ApiController]` pode ser aplicado a controladores específicos, como no exemplo a seguir do modelo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d97f2-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="d97f2-156">Atributo em vários controladores</span><span class="sxs-lookup"><span data-stu-id="d97f2-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="d97f2-157">Uma abordagem ao uso do atributo em mais de um controlador é a criação de uma classe de controlador base personalizada anotada com o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="d97f2-158">O exemplo a seguir mostra uma classe base personalizada e um controlador que deriva dele:</span><span class="sxs-lookup"><span data-stu-id="d97f2-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="d97f2-159">Atributo em um assembly</span><span class="sxs-lookup"><span data-stu-id="d97f2-159">Attribute on an assembly</span></span>

<span data-ttu-id="d97f2-160">Se [versão de compatibilidade](xref:mvc/compatibility-version) estiver definida como 2.2 ou posterior, o atributo `[ApiController]` poderá ser aplicado a um assembly.</span><span class="sxs-lookup"><span data-stu-id="d97f2-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="d97f2-161">A anotação dessa maneira aplica o comportamento da API Web para todos os controladores no assembly.</span><span class="sxs-lookup"><span data-stu-id="d97f2-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="d97f2-162">Não é possível recusar controladores individuais.</span><span class="sxs-lookup"><span data-stu-id="d97f2-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="d97f2-163">Aplique o atributo de nível de assembly à declaração de namespace em torno da classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="d97f2-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="d97f2-164">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="d97f2-164">Attribute routing requirement</span></span>

<span data-ttu-id="d97f2-165">O atributo `[ApiController]` transforma em requisito o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="d97f2-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="d97f2-166">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d97f2-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="d97f2-167">As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas por `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="d97f2-168">As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="d97f2-169">Respostas automáticas do HTTP 400</span><span class="sxs-lookup"><span data-stu-id="d97f2-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="d97f2-170">O atributo `[ApiController]` faz com que os erros de validação do modelo disparem automaticamente uma resposta HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="d97f2-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="d97f2-171">Consequentemente, o código a seguir se torna desnecessário em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="d97f2-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="d97f2-172">ASP.NET Core MVC usa o filtro de ação <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> para fazer a verificação anterior.</span><span class="sxs-lookup"><span data-stu-id="d97f2-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="d97f2-173">Resposta BadRequest padrão</span><span class="sxs-lookup"><span data-stu-id="d97f2-173">Default BadRequest response</span></span>

<span data-ttu-id="d97f2-174">Com uma versão de compatibilidade de 2,1, o tipo de resposta padrão para uma resposta HTTP 400 é <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="d97f2-175">O corpo da solicitação a seguir é um exemplo do tipo serializado:</span><span class="sxs-lookup"><span data-stu-id="d97f2-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d97f2-176">Com uma versão de compatibilidade do 2,2 ou posterior, o tipo de resposta padrão para uma resposta HTTP 400 é <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="d97f2-177">O corpo da solicitação a seguir é um exemplo do tipo serializado:</span><span class="sxs-lookup"><span data-stu-id="d97f2-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="d97f2-178">O tipo de `ValidationProblemDetails`:</span><span class="sxs-lookup"><span data-stu-id="d97f2-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="d97f2-179">Fornece um formato legível por máquina para especificar erros nas respostas da API Web.</span><span class="sxs-lookup"><span data-stu-id="d97f2-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="d97f2-180">Está em conformidade com a [especificação RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="d97f2-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="d97f2-181">Registrar respostas de 400 automática</span><span class="sxs-lookup"><span data-stu-id="d97f2-181">Log automatic 400 responses</span></span>

<span data-ttu-id="d97f2-182">Confira [Como registrar respostas de 400 automática sobre erros de validação de modelo (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="d97f2-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="d97f2-183">Desabilitar resposta automática 400</span><span class="sxs-lookup"><span data-stu-id="d97f2-183">Disable automatic 400 response</span></span>

<span data-ttu-id="d97f2-184">Para desabilitar o comportamento 400 automático, defina a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> como `true`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="d97f2-185">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d97f2-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="d97f2-186">Inferência de parâmetro de origem de associação</span><span class="sxs-lookup"><span data-stu-id="d97f2-186">Binding source parameter inference</span></span>

<span data-ttu-id="d97f2-187">Um atributo de origem de associação define o local no qual o valor do parâmetro de uma ação é encontrado.</span><span class="sxs-lookup"><span data-stu-id="d97f2-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="d97f2-188">Os seguintes atributos da origem da associação existem:</span><span class="sxs-lookup"><span data-stu-id="d97f2-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="d97f2-189">Atributo</span><span class="sxs-lookup"><span data-stu-id="d97f2-189">Attribute</span></span>|<span data-ttu-id="d97f2-190">Fonte de associação</span><span class="sxs-lookup"><span data-stu-id="d97f2-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="d97f2-191">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="d97f2-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="d97f2-192">Dados do formulário no corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="d97f2-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="d97f2-193">Cabeçalho da solicitação</span><span class="sxs-lookup"><span data-stu-id="d97f2-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="d97f2-194">Parâmetro de cadeia de caracteres de consulta de solicitação</span><span class="sxs-lookup"><span data-stu-id="d97f2-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="d97f2-195">Dados de rota da solicitação atual</span><span class="sxs-lookup"><span data-stu-id="d97f2-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="d97f2-196">O serviço de solicitação inserido como um parâmetro de ação</span><span class="sxs-lookup"><span data-stu-id="d97f2-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="d97f2-197">Não use `[FromRoute]` quando os valores puderem conter `%2f` (ou seja, `/`).</span><span class="sxs-lookup"><span data-stu-id="d97f2-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="d97f2-198">`%2f` não ficará sem escape para `/`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="d97f2-199">Use `[FromQuery]`, se o valor puder conter `%2f`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="d97f2-200">Sem o atributo `[ApiController]` ou outros atributos de origem da associação, como `[FromQuery]`, o runtime do ASP.NET Core tenta usar o associador de modelos de objeto complexo.</span><span class="sxs-lookup"><span data-stu-id="d97f2-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="d97f2-201">O associador de modelos de objeto complexo extrai os dados dos provedores de valor em uma ordem definida.</span><span class="sxs-lookup"><span data-stu-id="d97f2-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="d97f2-202">No exemplo a seguir, o atributo `[FromQuery]` indica que o valor do parâmetro `discontinuedOnly` é fornecido na cadeia de caracteres de consulta da URL de solicitação:</span><span class="sxs-lookup"><span data-stu-id="d97f2-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="d97f2-203">O atributo `[ApiController]` aplica regras de inferência para as fontes de dados padrão dos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="d97f2-204">Essas regras poupam você da necessidade de identificar as origens de associação manualmente aplicando atributos aos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="d97f2-205">As regras de inferência da origem de associação se comportam da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="d97f2-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="d97f2-206">`[FromBody]` é inferido para parâmetros de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="d97f2-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="d97f2-207">Uma exceção à regra de inferência `[FromBody]` é qualquer tipo interno complexo com um significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> e <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="d97f2-208">O código de inferência da origem da associação ignora esses tipos especiais.</span><span class="sxs-lookup"><span data-stu-id="d97f2-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="d97f2-209">`[FromForm]` é inferido para parâmetros de ação do tipo <xref:Microsoft.AspNetCore.Http.IFormFile> e <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="d97f2-210">Ele não é inferido para qualquer tipo simples ou definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="d97f2-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="d97f2-211">`[FromRoute]` é inferido para qualquer nome de parâmetro de ação correspondente a um parâmetro no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d97f2-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="d97f2-212">Quando mais de uma rota correspondem a um parâmetro de ação, qualquer valor de rota é considerado `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="d97f2-213">`[FromQuery]` é inferido para todos os outros parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="d97f2-214">Notas de inferência FromBody</span><span class="sxs-lookup"><span data-stu-id="d97f2-214">FromBody inference notes</span></span>

<span data-ttu-id="d97f2-215">`[FromBody]` não é inferido para tipos simples, como `string` ou `int`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="d97f2-216">Portanto, o atributo `[FromBody]` deve ser usado para tipos simples quando essa funcionalidade for necessária.</span><span class="sxs-lookup"><span data-stu-id="d97f2-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="d97f2-217">Quando uma ação tiver mais de um parâmetro associado ao corpo da solicitação, uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="d97f2-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="d97f2-218">Por exemplo, todas as assinaturas de método de ação a seguir causam uma exceção:</span><span class="sxs-lookup"><span data-stu-id="d97f2-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="d97f2-219">`[FromBody]` inferido em ambos, pois são tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="d97f2-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="d97f2-220">O atributo `[FromBody]` em um, inferido no outro, porque é um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="d97f2-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="d97f2-221">Atributo `[FromBody]` em ambos.</span><span class="sxs-lookup"><span data-stu-id="d97f2-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="d97f2-222">No ASP.NET Core 2.1, os parâmetros de tipo de coleção, como listas e matrizes, são inferidos incorretamente como `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="d97f2-223">O atributo `[FromBody]` deve ser usado para esses parâmetros se eles forem vinculados ao corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d97f2-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="d97f2-224">Esse comportamento é corrigido no ASP.NET Core 2.2 ou posterior, onde os parâmetros do tipo de coleção são inferidos para serem associados ao corpo por padrão.</span><span class="sxs-lookup"><span data-stu-id="d97f2-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="d97f2-225">Desabilitar regras de inferência</span><span class="sxs-lookup"><span data-stu-id="d97f2-225">Disable inference rules</span></span>

<span data-ttu-id="d97f2-226">Para desabilitar a inferência da origem da associação, defina <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> como `true`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="d97f2-227">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d97f2-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="d97f2-228">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="d97f2-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="d97f2-229">O atributo `[ApiController]` aplica uma regra de inferência quando um parâmetro de ação é anotado com o atributo [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) .</span><span class="sxs-lookup"><span data-stu-id="d97f2-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="d97f2-230">O tipo de conteúdo da solicitação `multipart/form-data` é inferido.</span><span class="sxs-lookup"><span data-stu-id="d97f2-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="d97f2-231">Para desabilitar o comportamento padrão, defina a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> como `true` em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d97f2-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="d97f2-232">Detalhes do problema dos códigos de status de erro</span><span class="sxs-lookup"><span data-stu-id="d97f2-232">Problem details for error status codes</span></span>

<span data-ttu-id="d97f2-233">Quando a versão de compatibilidade for 2.2 ou posterior, o MVC transformará um resultado de erro (um resultado com o código de status 400 ou superior) em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="d97f2-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="d97f2-234">O tipo `ProblemDetails` tem base na [especificação RFC 7807](https://tools.ietf.org/html/rfc7807) para fornecer detalhes de erro legíveis por computador em uma resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d97f2-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="d97f2-235">Considere o seguinte código em uma ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="d97f2-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="d97f2-236">O método `NotFound` produz um código de status HTTP 404 com um corpo de `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="d97f2-237">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d97f2-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="d97f2-238">Desabilitar a resposta de ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="d97f2-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="d97f2-239">A criação automática de uma instância de `ProblemDetails` é desabilitada quando a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-239">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="d97f2-240">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d97f2-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="d97f2-241">Definir tipos de conteúdo de solicitação com suporte com o atributo [Consumes]</span><span class="sxs-lookup"><span data-stu-id="d97f2-241">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="d97f2-242">Por padrão, uma ação dá suporte a todos os tipos de conteúdo de solicitação disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d97f2-242">By default, an action supports all available request content types.</span></span> <span data-ttu-id="d97f2-243">Por exemplo, se um aplicativo estiver configurado para dar suporte a [formatadores de entrada](xref:mvc/models/model-binding#input-formatters)JSON e XML, uma ação dará suporte a vários tipos de conteúdo, incluindo `application/json` e `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-243">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="d97f2-244">O atributo [[consume]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que uma ação limite os tipos de conteúdo de solicitação com suporte.</span><span class="sxs-lookup"><span data-stu-id="d97f2-244">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="d97f2-245">Aplique o atributo `[Consumes]` a uma ação ou controlador, especificando um ou mais tipos de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="d97f2-245">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="d97f2-246">No código anterior, a ação `CreateProduct` especifica o tipo de conteúdo `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-246">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="d97f2-247">As solicitações roteadas para esta ação devem especificar um cabeçalho de `Content-Type` de `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-247">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="d97f2-248">Solicitações que não especificam um cabeçalho de `Content-Type` de `application/xml` resultam em uma resposta de [tipo de mídia sem suporte 415](https://developer.mozilla.org/docs/Web/HTTP/Status/415) .</span><span class="sxs-lookup"><span data-stu-id="d97f2-248">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="d97f2-249">O atributo `[Consumes]` também permite que uma ação influencie sua seleção com base em um tipo de conteúdo de solicitação de entrada aplicando uma restrição de tipo.</span><span class="sxs-lookup"><span data-stu-id="d97f2-249">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="d97f2-250">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d97f2-250">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="d97f2-251">No código anterior, `ConsumesController` é configurado para tratar as solicitações enviadas para a URL de `https://localhost:5001/api/Consumes`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-251">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="d97f2-252">Ambas as ações do controlador, `PostJson` e `PostForm`, lidam com solicitações POST com a mesma URL.</span><span class="sxs-lookup"><span data-stu-id="d97f2-252">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="d97f2-253">Sem o atributo `[Consumes]` aplicando uma restrição de tipo, uma exceção de correspondência ambígua é gerada.</span><span class="sxs-lookup"><span data-stu-id="d97f2-253">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="d97f2-254">O atributo `[Consumes]` é aplicado a ambas as ações.</span><span class="sxs-lookup"><span data-stu-id="d97f2-254">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="d97f2-255">A ação de `PostJson` manipula solicitações enviadas com um cabeçalho de `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-255">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="d97f2-256">A ação de `PostForm` manipula solicitações enviadas com um cabeçalho de `Content-Type` de `application/x-www-form-urlencoded`.</span><span class="sxs-lookup"><span data-stu-id="d97f2-256">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="d97f2-257">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d97f2-257">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
