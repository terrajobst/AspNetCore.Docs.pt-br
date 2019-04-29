---
title: Criar APIs Web com o ASP.NET Core
author: scottaddie
description: Aprenda os fundamentos da criação de uma API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/11/2019
uid: web-api/index
ms.openlocfilehash: 334e5732269921a62356e7854824deccc051c291
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165164"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="36c40-103">Criar APIs Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36c40-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="36c40-104">Por [Scott Addie](https://github.com/scottaddie) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="36c40-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="36c40-105">O ASP.NET Core permite a criação de serviços RESTful, também conhecidos como APIs Web, usando C#.</span><span class="sxs-lookup"><span data-stu-id="36c40-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="36c40-106">Para lidar com solicitações, uma API Web usa controladores.</span><span class="sxs-lookup"><span data-stu-id="36c40-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="36c40-107">Em uma API Web, os *controladores* são classes que derivam de `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="36c40-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="36c40-108">Este artigo mostra como usar controladores para lidar com solicitações da API.</span><span class="sxs-lookup"><span data-stu-id="36c40-108">This article shows how to use controllers for handling API requests.</span></span>

<span data-ttu-id="36c40-109">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="36c40-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="36c40-110">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="36c40-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="36c40-111">Classe ControllerBase</span><span class="sxs-lookup"><span data-stu-id="36c40-111">ControllerBase class</span></span>

<span data-ttu-id="36c40-112">Uma API Web tem uma ou mais classes de controlador derivadas de <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="36c40-112">A web API has one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="36c40-113">Por exemplo, o modelo de projeto de API Web cria um controlador de Valores:</span><span class="sxs-lookup"><span data-stu-id="36c40-113">For example, the web API project template creates a Values controller:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

<span data-ttu-id="36c40-114">Não crie um controlador de API Web derivando da classe base <xref:Microsoft.AspNetCore.Mvc.Controller>.</span><span class="sxs-lookup"><span data-stu-id="36c40-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class.</span></span> <span data-ttu-id="36c40-115">`Controller` é derivado de `ControllerBase` e agrega suporte para exibições; portanto, serve para manipulação de páginas da Web, não para solicitações de API Web.</span><span class="sxs-lookup"><span data-stu-id="36c40-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span>  <span data-ttu-id="36c40-116">Há uma exceção a essa regra: se você pretende usar o mesmo controlador para exibições e APIs, derive-o de `Controller`.</span><span class="sxs-lookup"><span data-stu-id="36c40-116">There's an exception to this rule: if you plan to use the same controller for both views and APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="36c40-117">A classe `ControllerBase` fornece muitas propriedades e métodos úteis para lidar com solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="36c40-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="36c40-118">Por exemplo, `ControllerBase.CreatedAtAction` retorna um código de status 201:</span><span class="sxs-lookup"><span data-stu-id="36c40-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 <span data-ttu-id="36c40-119">Veja mais alguns exemplos de métodos fornecidos por `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="36c40-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="36c40-120">Método</span><span class="sxs-lookup"><span data-stu-id="36c40-120">Method</span></span>  |<span data-ttu-id="36c40-121">Observações</span><span class="sxs-lookup"><span data-stu-id="36c40-121">Notes</span></span>  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="36c40-122">Retorna o código de status 400.</span><span class="sxs-lookup"><span data-stu-id="36c40-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |<span data-ttu-id="36c40-123">Retorna o código de status 404.</span><span class="sxs-lookup"><span data-stu-id="36c40-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="36c40-124">Retorna um arquivo.</span><span class="sxs-lookup"><span data-stu-id="36c40-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="36c40-125">Invoca [model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="36c40-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="36c40-126">Invoca [validação de modelo](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="36c40-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="36c40-127">Confira uma lista com todos os métodos e propriedades disponíveis em <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="36c40-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="36c40-128">Atributos</span><span class="sxs-lookup"><span data-stu-id="36c40-128">Attributes</span></span>

<span data-ttu-id="36c40-129">O namespace <xref:Microsoft.AspNetCore.Mvc> fornece atributos que podem ser usados para configurar o comportamento de controladores de API Web e dos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="36c40-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="36c40-130">O exemplo a seguir usa atributos para especificar o método HTTP aceito e os códigos de status retornado:</span><span class="sxs-lookup"><span data-stu-id="36c40-130">The following example uses attributes to specify the HTTP method accepted and the status codes returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="36c40-131">Confira mais alguns exemplos de atributos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="36c40-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="36c40-132">Atributo</span><span class="sxs-lookup"><span data-stu-id="36c40-132">Attribute</span></span>|<span data-ttu-id="36c40-133">Observações</span><span class="sxs-lookup"><span data-stu-id="36c40-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="36c40-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="36c40-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="36c40-135">Especifica o padrão de URL para um controlador ou ação.</span><span class="sxs-lookup"><span data-stu-id="36c40-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="36c40-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="36c40-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="36c40-137">Especifica o prefixo e as propriedades que serão incluídos no model binding.</span><span class="sxs-lookup"><span data-stu-id="36c40-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="36c40-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="36c40-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="36c40-139">Identifica uma ação que dá suporte ao método HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="36c40-139">Identifies an action that supports the HTTP GET method.</span></span>|
|<span data-ttu-id="36c40-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="36c40-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="36c40-141">Especifica os tipos de dados aceitos por uma ação.</span><span class="sxs-lookup"><span data-stu-id="36c40-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="36c40-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="36c40-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="36c40-143">Especifica os tipos de dados retornados por uma ação.</span><span class="sxs-lookup"><span data-stu-id="36c40-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="36c40-144">Veja uma lista que inclui os atributos disponíveis no namespace <xref:Microsoft.AspNetCore.Mvc>.</span><span class="sxs-lookup"><span data-stu-id="36c40-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="36c40-145">Atributo ApiController</span><span class="sxs-lookup"><span data-stu-id="36c40-145">ApiController attribute</span></span>

<span data-ttu-id="36c40-146">O atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) pode ser aplicado a uma classe de controlador para habilitar comportamentos específicos à API:</span><span class="sxs-lookup"><span data-stu-id="36c40-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable API-specific behaviors:</span></span>

* [<span data-ttu-id="36c40-147">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="36c40-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="36c40-148">Respostas HTTP 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="36c40-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="36c40-149">Inferência de parâmetro de origem da associação</span><span class="sxs-lookup"><span data-stu-id="36c40-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="36c40-150">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="36c40-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="36c40-151">Detalhes do problema dos códigos de status de erro</span><span class="sxs-lookup"><span data-stu-id="36c40-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="36c40-152">Esses recursos exigem [compatibilidade com a versão](<xref:mvc/compatibility-version>) 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="36c40-152">These features require a [compatibility version](<xref:mvc/compatibility-version>) of 2.1 or later.</span></span>

### <a name="apicontroller-on-specific-controllers"></a><span data-ttu-id="36c40-153">ApiController em controladores específicos</span><span class="sxs-lookup"><span data-stu-id="36c40-153">ApiController on specific controllers</span></span>

<span data-ttu-id="36c40-154">O atributo `[ApiController]` pode ser aplicado a controladores específicos, como no exemplo a seguir do modelo de projeto:</span><span class="sxs-lookup"><span data-stu-id="36c40-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a><span data-ttu-id="36c40-155">ApiController em vários controladores</span><span class="sxs-lookup"><span data-stu-id="36c40-155">ApiController on multiple controllers</span></span>

<span data-ttu-id="36c40-156">Uma abordagem ao uso do atributo em mais de um controlador é a criação de uma classe de controlador base personalizada anotada com o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="36c40-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="36c40-157">Veja um exemplo que mostra uma classe base personalizada e um controlador derivado dela:</span><span class="sxs-lookup"><span data-stu-id="36c40-157">Here's an example showing a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a><span data-ttu-id="36c40-158">ApiController em um assembly</span><span class="sxs-lookup"><span data-stu-id="36c40-158">ApiController on an assembly</span></span>

<span data-ttu-id="36c40-159">Se [versão de compatibilidade](<xref:mvc/compatibility-version>) estiver definida como 2.2 ou posterior, o atributo `[ApiController]` poderá ser aplicado a um assembly.</span><span class="sxs-lookup"><span data-stu-id="36c40-159">If [compatibility version](<xref:mvc/compatibility-version>) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="36c40-160">A anotação dessa maneira aplica o comportamento da API Web para todos os controladores no assembly.</span><span class="sxs-lookup"><span data-stu-id="36c40-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="36c40-161">Não é possível recusar controladores individuais.</span><span class="sxs-lookup"><span data-stu-id="36c40-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="36c40-162">Aplique o atributo no nível do assembly à classe `Startup` conforme mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="36c40-162">Apply the assembly-level attribute to the `Startup` class as shown in the following example:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="36c40-163">Requisito de roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="36c40-163">Attribute routing requirement</span></span>

<span data-ttu-id="36c40-164">O atributo `ApiController` transforma em requisito o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="36c40-164">The `ApiController` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="36c40-165">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="36c40-165">For example:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

<span data-ttu-id="36c40-166">As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="36c40-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

## <a name="automatic-http-400-responses"></a><span data-ttu-id="36c40-167">Respostas automáticas do HTTP 400</span><span class="sxs-lookup"><span data-stu-id="36c40-167">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="36c40-168">O atributo `ApiController` faz com que os erros de validação do modelo disparem automaticamente uma resposta HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="36c40-168">The `ApiController` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="36c40-169">Consequentemente, o código a seguir se torna desnecessário em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="36c40-169">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a><span data-ttu-id="36c40-170">Resposta BadRequest padrão</span><span class="sxs-lookup"><span data-stu-id="36c40-170">Default BadRequest response</span></span> 

<span data-ttu-id="36c40-171">Com uma versão de compatibilidade de 2.2 ou posterior, o tipo de resposta padrão para respostas HTTP 400 é <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="36c40-171">With a compatibility version of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="36c40-172">O tipo `ValidationProblemDetails` está em conformidade com a [especificação RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="36c40-172">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="36c40-173">Para alterar a resposta padrão para <xref:Microsoft.AspNetCore.Mvc.SerializableError>, defina a propriedade `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` como `true` em `Startup.ConfigureServices`, conforme mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="36c40-173">To change the default response to <xref:Microsoft.AspNetCore.Mvc.SerializableError>, set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a><span data-ttu-id="36c40-174">Personalizar a resposta de BadRequest</span><span class="sxs-lookup"><span data-stu-id="36c40-174">Customize BadRequest response</span></span>

<span data-ttu-id="36c40-175">Para personalizar a resposta resultante de um erro de validação, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span><span class="sxs-lookup"><span data-stu-id="36c40-175">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="36c40-176">Adicione o código realçado a seguir após `services.AddMvc().SetCompatibilityVersion`:</span><span class="sxs-lookup"><span data-stu-id="36c40-176">Add the following highlighted code after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="disable-automatic-400"></a><span data-ttu-id="36c40-177">Desabilitar 400 automática</span><span class="sxs-lookup"><span data-stu-id="36c40-177">Disable automatic 400</span></span>

<span data-ttu-id="36c40-178">Para desabilitar o comportamento 400 automático, defina a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> como `true`.</span><span class="sxs-lookup"><span data-stu-id="36c40-178">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="36c40-179">Adicione o código realçado a seguir em `Startup.ConfigureServices` após `services.AddMvc().SetCompatibilityVersion`:</span><span class="sxs-lookup"><span data-stu-id="36c40-179">Add the following highlighted code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="36c40-180">Inferência de parâmetro de origem de associação</span><span class="sxs-lookup"><span data-stu-id="36c40-180">Binding source parameter inference</span></span>

<span data-ttu-id="36c40-181">Um atributo de origem de associação define o local no qual o valor do parâmetro de uma ação é encontrado.</span><span class="sxs-lookup"><span data-stu-id="36c40-181">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="36c40-182">Os seguintes atributos da origem da associação existem:</span><span class="sxs-lookup"><span data-stu-id="36c40-182">The following binding source attributes exist:</span></span>

|<span data-ttu-id="36c40-183">Atributo</span><span class="sxs-lookup"><span data-stu-id="36c40-183">Attribute</span></span>|<span data-ttu-id="36c40-184">Fonte de associação</span><span class="sxs-lookup"><span data-stu-id="36c40-184">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="36c40-185">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="36c40-185">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="36c40-186">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="36c40-186">Request body</span></span> |
|<span data-ttu-id="36c40-187">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="36c40-187">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="36c40-188">Dados do formulário no corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="36c40-188">Form data in the request body</span></span> |
|<span data-ttu-id="36c40-189">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="36c40-189">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="36c40-190">Cabeçalho da solicitação</span><span class="sxs-lookup"><span data-stu-id="36c40-190">Request header</span></span> |
|<span data-ttu-id="36c40-191">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="36c40-191">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="36c40-192">Parâmetro de cadeia de caracteres de consulta de solicitação</span><span class="sxs-lookup"><span data-stu-id="36c40-192">Request query string parameter</span></span> |
|<span data-ttu-id="36c40-193">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="36c40-193">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="36c40-194">Dados de rota da solicitação atual</span><span class="sxs-lookup"><span data-stu-id="36c40-194">Route data from the current request</span></span> |
|<span data-ttu-id="36c40-195">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="36c40-195">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="36c40-196">O serviço de solicitação inserido como um parâmetro de ação</span><span class="sxs-lookup"><span data-stu-id="36c40-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="36c40-197">Não use `[FromRoute]` quando os valores puderem conter `%2f` (ou seja, `/`).</span><span class="sxs-lookup"><span data-stu-id="36c40-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="36c40-198">`%2f` não ficará sem escape para `/`.</span><span class="sxs-lookup"><span data-stu-id="36c40-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="36c40-199">Use `[FromQuery]`, se o valor puder conter `%2f`.</span><span class="sxs-lookup"><span data-stu-id="36c40-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="36c40-200">Sem o atributo `[ApiController]` ou outros atributos de origem da associação, como `[FromQuery]`, o tempo de execução do ASP.NET Core tenta usar o associador de modelos de objeto complexo.</span><span class="sxs-lookup"><span data-stu-id="36c40-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="36c40-201">O associador de modelos de objeto complexo extrai os dados dos provedores de valor em uma ordem definida.</span><span class="sxs-lookup"><span data-stu-id="36c40-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="36c40-202">No exemplo a seguir, o atributo `[FromQuery]` indica que o valor do parâmetro `discontinuedOnly` é fornecido na cadeia de caracteres de consulta da URL de solicitação:</span><span class="sxs-lookup"><span data-stu-id="36c40-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="36c40-203">O atributo `[ApiController]` aplica regras de inferência para as fontes de dados padrão dos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="36c40-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="36c40-204">Essas regras poupam você da necessidade de identificar as origens de associação manualmente aplicando atributos aos parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="36c40-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="36c40-205">As regras de inferência da origem de associação se comportam da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="36c40-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="36c40-206">`[FromBody]` é inferido para parâmetros de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="36c40-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="36c40-207">Uma exceção à regra de inferência `[FromBody]` é qualquer tipo interno complexo com um significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> e <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="36c40-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="36c40-208">O código de inferência da origem da associação ignora esses tipos especiais.</span><span class="sxs-lookup"><span data-stu-id="36c40-208">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="36c40-209">`[FromForm]` é inferido para parâmetros de ação do tipo <xref:Microsoft.AspNetCore.Http.IFormFile> e <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="36c40-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="36c40-210">Ele não é inferido para qualquer tipo simples ou definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="36c40-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="36c40-211">`[FromRoute]` é inferido para qualquer nome de parâmetro de ação correspondente a um parâmetro no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="36c40-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="36c40-212">Quando mais de uma rota correspondem a um parâmetro de ação, qualquer valor de rota é considerado `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="36c40-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="36c40-213">`[FromQuery]` é inferido para todos os outros parâmetros de ação.</span><span class="sxs-lookup"><span data-stu-id="36c40-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="36c40-214">Notas de inferência FromBody</span><span class="sxs-lookup"><span data-stu-id="36c40-214">FromBody inference notes</span></span>

<span data-ttu-id="36c40-215">`[FromBody]` não é inferido para tipos simples, como `string` ou `int`.</span><span class="sxs-lookup"><span data-stu-id="36c40-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="36c40-216">Portanto, o atributo `[FromBody]` deve ser usado para tipos simples quando essa funcionalidade for necessária.</span><span class="sxs-lookup"><span data-stu-id="36c40-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="36c40-217">Quando uma ação tiver mais de um parâmetro associado ao corpo da solicitação, uma exceção será lançada.</span><span class="sxs-lookup"><span data-stu-id="36c40-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="36c40-218">Por exemplo, todas as assinaturas de método de ação a seguir causam uma exceção:</span><span class="sxs-lookup"><span data-stu-id="36c40-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="36c40-219">`[FromBody]` inferido em ambos, pois são tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="36c40-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="36c40-220">O atributo `[FromBody]` em um, inferido no outro, porque é um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="36c40-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="36c40-221">Atributo `[FromBody]` em ambos.</span><span class="sxs-lookup"><span data-stu-id="36c40-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> <span data-ttu-id="36c40-222">No ASP.NET Core 2.1, os parâmetros de tipo de coleção, como listas e matrizes, são inferidos incorretamente como `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="36c40-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="36c40-223">O atributo `[FromBody]` deve ser usado para esses parâmetros se eles forem vinculados ao corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="36c40-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="36c40-224">Esse comportamento é corrigido no ASP.NET Core 2.2 ou posterior, onde os parâmetros do tipo de coleção são inferidos para serem associados ao corpo por padrão.</span><span class="sxs-lookup"><span data-stu-id="36c40-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

### <a name="disable-inference-rules"></a><span data-ttu-id="36c40-225">Desabilitar regras de inferência</span><span class="sxs-lookup"><span data-stu-id="36c40-225">Disable inference rules</span></span>

<span data-ttu-id="36c40-226">Para desabilitar a inferência da origem da associação, defina <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> como `true`.</span><span class="sxs-lookup"><span data-stu-id="36c40-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="36c40-227">Adicione o seguinte código em `Startup.ConfigureServices` após `services.AddMvc().SetCompatibilityVersion`:</span><span class="sxs-lookup"><span data-stu-id="36c40-227">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="36c40-228">Inferência de solicitação de várias partes/dados de formulário</span><span class="sxs-lookup"><span data-stu-id="36c40-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="36c40-229">O atributo `[ApiController]` aplicar uma regra de inferência quando um parâmetro de ação é anotado com o atributo [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute): o tipo de conteúdo de solicitação `multipart/form-data` é inferido.</span><span class="sxs-lookup"><span data-stu-id="36c40-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute: the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="36c40-230">Para desabilitar o comportamento padrão, defina <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> como `true` em `Startup.ConfigureServices`, conforme mostra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="36c40-230">To disable the default behavior, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>  to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="36c40-231">Detalhes do problema dos códigos de status de erro</span><span class="sxs-lookup"><span data-stu-id="36c40-231">Problem details for error status codes</span></span>

<span data-ttu-id="36c40-232">Quando a versão de compatibilidade for 2.2 ou posterior, o MVC transformará um resultado de erro (um resultado com o código de status 400 ou superior) em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="36c40-232">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="36c40-233">O tipo `ProblemDetails` tem base na [especificação RFC 7807](https://tools.ietf.org/html/rfc7807) para fornecer detalhes de erro legíveis por computador em uma resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="36c40-233">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="36c40-234">Considere o seguinte código em uma ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="36c40-234">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="36c40-235">A resposta HTTP para `NotFound` tem um código de status 404 com um corpo `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="36c40-235">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="36c40-236">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="36c40-236">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="36c40-237">Personalizar a resposta de ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="36c40-237">Customize ProblemDetails response</span></span>

<span data-ttu-id="36c40-238">Use a propriedade `ClientErrorMapping` para configurar o conteúdo da resposta `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="36c40-238">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="36c40-239">Por exemplo, o código a seguir atualiza a propriedade `type` para respostas 404:</span><span class="sxs-lookup"><span data-stu-id="36c40-239">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a><span data-ttu-id="36c40-240">Desabilitar a resposta de ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="36c40-240">Disable ProblemDetails response</span></span>

<span data-ttu-id="36c40-241">A criação automática de `ProblemDetails` fica desabilitada quando a propriedade `SuppressMapClientErrors` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="36c40-241">The automatic creation of `ProblemDetails` is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="36c40-242">Adicione o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36c40-242">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a><span data-ttu-id="36c40-243">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="36c40-243">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
