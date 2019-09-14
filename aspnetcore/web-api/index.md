---
title: Criar APIs Web com o ASP.NET Core
author: scottaddie
description: Aprenda os fundamentos da criação de uma API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2019
uid: web-api/index
ms.openlocfilehash: 6e1868690a2c384307a23c89467505d3ed8916db
ms.sourcegitcommit: 805f625d16d74e77f02f5f37326e5aceafcb78e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985470"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="9f92c-103">Criar APIs Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f92c-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="9f92c-104">Por [Scott Addie](https://github.com/scottaddie) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9f92c-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="9f92c-105">O ASP.NET Core permite a criação de serviços RESTful, também conhecidos como APIs Web, usando C#.</span><span class="sxs-lookup"><span data-stu-id="9f92c-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="9f92c-106">Para lidar com solicitações, uma API Web usa controladores.</span><span class="sxs-lookup"><span data-stu-id="9f92c-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="9f92c-107">Em uma API Web, os *controladores* são classes que derivam de `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="9f92c-108">Este artigo mostra como usar controladores para manipular solicitações de API da Web.</span><span class="sxs-lookup"><span data-stu-id="9f92c-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="9f92c-109">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="9f92c-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="9f92c-110">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="9f92c-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="9f92c-111">Classe ControllerBase</span><span class="sxs-lookup"><span data-stu-id="9f92c-111">ControllerBase class</span></span>

<span data-ttu-id="9f92c-112">Uma API da Web consiste em uma ou mais classes de controlador que <xref:Microsoft.AspNetCore.Mvc.ControllerBase>derivam de.</span><span class="sxs-lookup"><span data-stu-id="9f92c-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="9f92c-113">O modelo de projeto de API Web fornece um controlador de início:</span><span class="sxs-lookup"><span data-stu-id="9f92c-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="9f92c-114">Não crie um controlador de API Web derivando da classe base <xref:Microsoft.AspNetCore.Mvc.Controller>.</span><span class="sxs-lookup"><span data-stu-id="9f92c-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="9f92c-115">`Controller` é derivado de `ControllerBase` e agrega suporte para exibições; portanto, serve para manipulação de páginas da Web, não para solicitações de API Web.</span><span class="sxs-lookup"><span data-stu-id="9f92c-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="9f92c-116">Há uma exceção a essa regra: se você planeja usar o mesmo controlador para exibições e APIs da Web, derive-o `Controller`de.</span><span class="sxs-lookup"><span data-stu-id="9f92c-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="9f92c-117">A classe `ControllerBase` fornece muitas propriedades e métodos úteis para lidar com solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f92c-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="9f92c-118">Por exemplo, `ControllerBase.CreatedAtAction` retorna um código de status 201:</span><span class="sxs-lookup"><span data-stu-id="9f92c-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="9f92c-119">Veja mais alguns exemplos de métodos fornecidos por `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="9f92c-120">Método</span><span class="sxs-lookup"><span data-stu-id="9f92c-120">Method</span></span>   |<span data-ttu-id="9f92c-121">Observações</span><span class="sxs-lookup"><span data-stu-id="9f92c-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="9f92c-122">Retorna o código de status 400.</span><span class="sxs-lookup"><span data-stu-id="9f92c-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>|<span data-ttu-id="9f92c-123">Retorna o código de status 404.</span><span class="sxs-lookup"><span data-stu-id="9f92c-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="9f92c-124">Retorna um arquivo.</span><span class="sxs-lookup"><span data-stu-id="9f92c-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="9f92c-125">Invoca [model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="9f92c-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="9f92c-126">Invoca [validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="9f92c-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="9f92c-127">Confira uma lista com todos os métodos e propriedades disponíveis em <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="9f92c-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="9f92c-128">Atributos</span><span class="sxs-lookup"><span data-stu-id="9f92c-128">Attributes</span></span>

<span data-ttu-id="9f92c-129">O namespace <xref:Microsoft.AspNetCore.Mvc> fornece atributos que podem ser usados para configurar o comportamento de controladores de API Web e dos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="9f92c-130">O exemplo a seguir usa atributos para especificar o verbo de ação HTTP com suporte e quaisquer códigos de status HTTP conhecidos que poderiam ser retornados:</span><span class="sxs-lookup"><span data-stu-id="9f92c-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="9f92c-131">Confira mais alguns exemplos de atributos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="9f92c-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="9f92c-132">Atributo</span><span class="sxs-lookup"><span data-stu-id="9f92c-132">Attribute</span></span>|<span data-ttu-id="9f92c-133">Observações</span><span class="sxs-lookup"><span data-stu-id="9f92c-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="9f92c-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9f92c-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="9f92c-135">Especifica o padrão de URL para um controlador ou ação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="9f92c-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9f92c-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="9f92c-137">Especifica o prefixo e as propriedades que serão incluídos no model binding.</span><span class="sxs-lookup"><span data-stu-id="9f92c-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="9f92c-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9f92c-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="9f92c-139">Identifica uma ação que dá suporte ao verbo de ação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9f92c-139">Identifies an action that supports the HTTP GET action verb.</span></span>|
|<span data-ttu-id="9f92c-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9f92c-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="9f92c-141">Especifica os tipos de dados aceitos por uma ação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="9f92c-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9f92c-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="9f92c-143">Especifica os tipos de dados retornados por uma ação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="9f92c-144">Veja uma lista que inclui os atributos disponíveis no namespace <xref:Microsoft.AspNetCore.Mvc>.</span><span class="sxs-lookup"><span data-stu-id="9f92c-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="9f92c-145">Atributo ApiController</span><span class="sxs-lookup"><span data-stu-id="9f92c-145">ApiController attribute</span></span>

<span data-ttu-id="9f92c-146">O atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) pode ser aplicado a uma classe de controlador para habilitar os seguintes conceituada, comportamentos específicos de API:</span><span class="sxs-lookup"><span data-stu-id="9f92c-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

* [<span data-ttu-id="9f92c-147">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="9f92c-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="9f92c-148">Respostas HTTP 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="9f92c-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="9f92c-149">Inferência de parâmetro de origem da associação</span><span class="sxs-lookup"><span data-stu-id="9f92c-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="9f92c-150">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="9f92c-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="9f92c-151">Detalhes do problema dos códigos de status de erro</span><span class="sxs-lookup"><span data-stu-id="9f92c-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="9f92c-152">Esses recursos exigem [compatibilidade com a versão](xref:mvc/compatibility-version) 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="9f92c-152">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="9f92c-153">Atributo em controladores específicos</span><span class="sxs-lookup"><span data-stu-id="9f92c-153">Attribute on specific controllers</span></span>

<span data-ttu-id="9f92c-154">O atributo `[ApiController]` pode ser aplicado a controladores específicos, como no exemplo a seguir do modelo de projeto:</span><span class="sxs-lookup"><span data-stu-id="9f92c-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="9f92c-155">Atributo em vários controladores</span><span class="sxs-lookup"><span data-stu-id="9f92c-155">Attribute on multiple controllers</span></span>

<span data-ttu-id="9f92c-156">Uma abordagem ao uso do atributo em mais de um controlador é a criação de uma classe de controlador base personalizada anotada com o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="9f92c-157">O exemplo a seguir mostra uma classe base personalizada e um controlador que deriva dele:</span><span class="sxs-lookup"><span data-stu-id="9f92c-157">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="9f92c-158">Atributo em um assembly</span><span class="sxs-lookup"><span data-stu-id="9f92c-158">Attribute on an assembly</span></span>

<span data-ttu-id="9f92c-159">Se [versão de compatibilidade](xref:mvc/compatibility-version) estiver definida como 2.2 ou posterior, o atributo `[ApiController]` poderá ser aplicado a um assembly.</span><span class="sxs-lookup"><span data-stu-id="9f92c-159">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="9f92c-160">A anotação dessa maneira aplica o comportamento da API Web para todos os controladores no assembly.</span><span class="sxs-lookup"><span data-stu-id="9f92c-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="9f92c-161">Não é possível recusar controladores individuais.</span><span class="sxs-lookup"><span data-stu-id="9f92c-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="9f92c-162">Aplique o atributo de nível de assembly à declaração de namespace em `Startup` torno da classe:</span><span class="sxs-lookup"><span data-stu-id="9f92c-162">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="9f92c-163">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="9f92c-163">Attribute routing requirement</span></span>

<span data-ttu-id="9f92c-164">O atributo `[ApiController]` transforma em requisito o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="9f92c-164">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="9f92c-165">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9f92c-165">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="9f92c-166">As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas `UseEndpoints`pelo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> , `Startup.Configure` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>ou no.</span><span class="sxs-lookup"><span data-stu-id="9f92c-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="9f92c-167">As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="9f92c-168">Respostas automáticas do HTTP 400</span><span class="sxs-lookup"><span data-stu-id="9f92c-168">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="9f92c-169">O atributo `[ApiController]` faz com que os erros de validação do modelo disparem automaticamente uma resposta HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="9f92c-169">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="9f92c-170">Consequentemente, o código a seguir se torna desnecessário em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="9f92c-170">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="9f92c-171">ASP.NET Core MVC usa o <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> filtro de ação para fazer a verificação anterior.</span><span class="sxs-lookup"><span data-stu-id="9f92c-171">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="9f92c-172">Resposta BadRequest padrão</span><span class="sxs-lookup"><span data-stu-id="9f92c-172">Default BadRequest response</span></span> 

<span data-ttu-id="9f92c-173">Com uma versão de compatibilidade de 2,1, o tipo de resposta padrão para uma resposta HTTP <xref:Microsoft.AspNetCore.Mvc.SerializableError>400 é.</span><span class="sxs-lookup"><span data-stu-id="9f92c-173">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="9f92c-174">O corpo da solicitação a seguir é um exemplo do tipo serializado:</span><span class="sxs-lookup"><span data-stu-id="9f92c-174">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9f92c-175">Com uma versão de compatibilidade do 2,2 ou posterior, o tipo de resposta padrão para uma resposta HTTP <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>400 é.</span><span class="sxs-lookup"><span data-stu-id="9f92c-175">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="9f92c-176">O corpo da solicitação a seguir é um exemplo do tipo serializado:</span><span class="sxs-lookup"><span data-stu-id="9f92c-176">The following request body is an example of the serialized type:</span></span>

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

<span data-ttu-id="9f92c-177">O `ValidationProblemDetails` tipo:</span><span class="sxs-lookup"><span data-stu-id="9f92c-177">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="9f92c-178">Fornece um formato legível por máquina para especificar erros nas respostas da API Web.</span><span class="sxs-lookup"><span data-stu-id="9f92c-178">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="9f92c-179">Está em conformidade com a [especificação RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="9f92c-179">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="9f92c-180">Para alterar o tipo de resposta padrão `SerializableError`para, aplique as alterações realçadas em: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9f92c-180">To change the default response type to `SerializableError`, apply the highlighted changes in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

### <a name="customize-badrequest-response"></a><span data-ttu-id="9f92c-181">Personalizar a resposta de BadRequest</span><span class="sxs-lookup"><span data-stu-id="9f92c-181">Customize BadRequest response</span></span>

<span data-ttu-id="9f92c-182">Para personalizar a resposta resultante de um erro de validação, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span><span class="sxs-lookup"><span data-stu-id="9f92c-182">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="9f92c-183">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9f92c-183">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=4-20)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=5-21)]

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="9f92c-184">Registrar respostas de 400 automática</span><span class="sxs-lookup"><span data-stu-id="9f92c-184">Log automatic 400 responses</span></span>

<span data-ttu-id="9f92c-185">Confira [Como registrar respostas de 400 automática sobre erros de validação de modelo (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="9f92c-185">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="9f92c-186">Desabilitar resposta automática 400</span><span class="sxs-lookup"><span data-stu-id="9f92c-186">Disable automatic 400 response</span></span>

<span data-ttu-id="9f92c-187">Para desabilitar o comportamento 400 automático, defina a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> como `true`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-187">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="9f92c-188">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f92c-188">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="9f92c-189">Inferência de parâmetro de origem de associação</span><span class="sxs-lookup"><span data-stu-id="9f92c-189">Binding source parameter inference</span></span>

<span data-ttu-id="9f92c-190">Um atributo de origem de associação define o local no qual o valor do parâmetro de uma ação é encontrado.</span><span class="sxs-lookup"><span data-stu-id="9f92c-190">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="9f92c-191">Os seguintes atributos da origem da associação existem:</span><span class="sxs-lookup"><span data-stu-id="9f92c-191">The following binding source attributes exist:</span></span>

|<span data-ttu-id="9f92c-192">Atributo</span><span class="sxs-lookup"><span data-stu-id="9f92c-192">Attribute</span></span>|<span data-ttu-id="9f92c-193">Fonte de associação</span><span class="sxs-lookup"><span data-stu-id="9f92c-193">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="9f92c-194">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="9f92c-194">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="9f92c-195">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="9f92c-195">Request body</span></span> |
|<span data-ttu-id="9f92c-196">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="9f92c-196">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="9f92c-197">Dados do formulário no corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="9f92c-197">Form data in the request body</span></span> |
|<span data-ttu-id="9f92c-198">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="9f92c-198">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="9f92c-199">Cabeçalho da solicitação</span><span class="sxs-lookup"><span data-stu-id="9f92c-199">Request header</span></span> |
|<span data-ttu-id="9f92c-200">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="9f92c-200">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="9f92c-201">Parâmetro de cadeia de caracteres de consulta de solicitação</span><span class="sxs-lookup"><span data-stu-id="9f92c-201">Request query string parameter</span></span> |
|<span data-ttu-id="9f92c-202">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="9f92c-202">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="9f92c-203">Dados de rota da solicitação atual</span><span class="sxs-lookup"><span data-stu-id="9f92c-203">Route data from the current request</span></span> |
|<span data-ttu-id="9f92c-204">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="9f92c-204">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="9f92c-205">O serviço de solicitação inserido como um parâmetro de ação</span><span class="sxs-lookup"><span data-stu-id="9f92c-205">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="9f92c-206">Não use `[FromRoute]` quando os valores puderem conter `%2f` (ou seja, `/`).</span><span class="sxs-lookup"><span data-stu-id="9f92c-206">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="9f92c-207">`%2f` não ficará sem escape para `/`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-207">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="9f92c-208">Use `[FromQuery]`, se o valor puder conter `%2f`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-208">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="9f92c-209">Sem o atributo `[ApiController]` ou outros atributos de origem da associação, como `[FromQuery]`, o tempo de execução do ASP.NET Core tenta usar o associador de modelos de objeto complexo.</span><span class="sxs-lookup"><span data-stu-id="9f92c-209">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="9f92c-210">O associador de modelos de objeto complexo extrai os dados dos provedores de valor em uma ordem definida.</span><span class="sxs-lookup"><span data-stu-id="9f92c-210">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="9f92c-211">No exemplo a seguir, o atributo `[FromQuery]` indica que o valor do parâmetro `discontinuedOnly` é fornecido na cadeia de caracteres de consulta da URL de solicitação:</span><span class="sxs-lookup"><span data-stu-id="9f92c-211">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="9f92c-212">O atributo `[ApiController]` aplica regras de inferência para as fontes de dados padrão dos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-212">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="9f92c-213">Essas regras poupam você da necessidade de identificar as origens de associação manualmente aplicando atributos aos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-213">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="9f92c-214">As regras de inferência da origem de associação se comportam da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="9f92c-214">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="9f92c-215">`[FromBody]` é inferido para parâmetros de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="9f92c-215">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="9f92c-216">Uma exceção à regra de inferência `[FromBody]` é qualquer tipo interno complexo com um significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> e <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="9f92c-216">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="9f92c-217">O código de inferência da origem da associação ignora esses tipos especiais.</span><span class="sxs-lookup"><span data-stu-id="9f92c-217">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="9f92c-218">`[FromForm]` é inferido para parâmetros de ação do tipo <xref:Microsoft.AspNetCore.Http.IFormFile> e <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="9f92c-218">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="9f92c-219">Ele não é inferido para qualquer tipo simples ou definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="9f92c-219">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="9f92c-220">`[FromRoute]` é inferido para qualquer nome de parâmetro de ação correspondente a um parâmetro no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9f92c-220">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="9f92c-221">Quando mais de uma rota correspondem a um parâmetro de ação, qualquer valor de rota é considerado `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-221">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="9f92c-222">`[FromQuery]` é inferido para todos os outros parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-222">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="9f92c-223">Notas de inferência FromBody</span><span class="sxs-lookup"><span data-stu-id="9f92c-223">FromBody inference notes</span></span>

<span data-ttu-id="9f92c-224">`[FromBody]` não é inferido para tipos simples, como `string` ou `int`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-224">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="9f92c-225">Portanto, o atributo `[FromBody]` deve ser usado para tipos simples quando essa funcionalidade for necessária.</span><span class="sxs-lookup"><span data-stu-id="9f92c-225">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="9f92c-226">Quando uma ação tiver mais de um parâmetro associado ao corpo da solicitação, uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="9f92c-226">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="9f92c-227">Por exemplo, todas as assinaturas de método de ação a seguir causam uma exceção:</span><span class="sxs-lookup"><span data-stu-id="9f92c-227">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="9f92c-228">`[FromBody]` inferido em ambos, pois são tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="9f92c-228">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="9f92c-229">O atributo `[FromBody]` em um, inferido no outro, porque é um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="9f92c-229">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="9f92c-230">Atributo `[FromBody]` em ambos.</span><span class="sxs-lookup"><span data-stu-id="9f92c-230">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="9f92c-231">No ASP.NET Core 2.1, os parâmetros de tipo de coleção, como listas e matrizes, são inferidos incorretamente como `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-231">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="9f92c-232">O atributo `[FromBody]` deve ser usado para esses parâmetros se eles forem vinculados ao corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9f92c-232">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="9f92c-233">Esse comportamento é corrigido no ASP.NET Core 2.2 ou posterior, onde os parâmetros do tipo de coleção são inferidos para serem associados ao corpo por padrão.</span><span class="sxs-lookup"><span data-stu-id="9f92c-233">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="9f92c-234">Desabilitar regras de inferência</span><span class="sxs-lookup"><span data-stu-id="9f92c-234">Disable inference rules</span></span>

<span data-ttu-id="9f92c-235">Para desabilitar a inferência da origem da associação, defina <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> como `true`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-235">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="9f92c-236">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f92c-236">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="9f92c-237">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="9f92c-237">Multipart/form-data request inference</span></span>

<span data-ttu-id="9f92c-238">O `[ApiController]` atributo aplica uma regra de inferência quando um parâmetro de ação é anotado com o atributo [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) .</span><span class="sxs-lookup"><span data-stu-id="9f92c-238">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="9f92c-239">O `multipart/form-data` tipo de conteúdo da solicitação é inferido.</span><span class="sxs-lookup"><span data-stu-id="9f92c-239">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="9f92c-240">Para desabilitar o comportamento padrão, defina a <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> Propriedade como `true` em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f92c-240">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="9f92c-241">Detalhes do problema dos códigos de status de erro</span><span class="sxs-lookup"><span data-stu-id="9f92c-241">Problem details for error status codes</span></span>

<span data-ttu-id="9f92c-242">Quando a versão de compatibilidade for 2.2 ou posterior, o MVC transformará um resultado de erro (um resultado com o código de status 400 ou superior) em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="9f92c-242">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="9f92c-243">O tipo `ProblemDetails` tem base na [especificação RFC 7807](https://tools.ietf.org/html/rfc7807) para fornecer detalhes de erro legíveis por computador em uma resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f92c-243">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="9f92c-244">Considere o seguinte código em uma ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="9f92c-244">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="9f92c-245">O `NotFound` método produz um código de status http 404 com `ProblemDetails` um corpo.</span><span class="sxs-lookup"><span data-stu-id="9f92c-245">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="9f92c-246">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9f92c-246">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="9f92c-247">Personalizar a resposta de ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="9f92c-247">Customize ProblemDetails response</span></span>

<span data-ttu-id="9f92c-248">Use a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> para configurar o conteúdo da resposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="9f92c-248">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="9f92c-249">Por exemplo, o código a seguir atualiza a propriedade `type` para respostas 404:</span><span class="sxs-lookup"><span data-stu-id="9f92c-249">For example, the following code updates the `type` property for 404 responses:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

### <a name="disable-problemdetails-response"></a><span data-ttu-id="9f92c-250">Desabilitar a resposta de ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="9f92c-250">Disable ProblemDetails response</span></span>

<span data-ttu-id="9f92c-251">A criação automática de uma `ProblemDetails` instância é desabilitada quando <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> a propriedade é definida `true`como.</span><span class="sxs-lookup"><span data-stu-id="9f92c-251">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> property is set to `true`.</span></span> <span data-ttu-id="9f92c-252">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f92c-252">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9f92c-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9f92c-253">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
