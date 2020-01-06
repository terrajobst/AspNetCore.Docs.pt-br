---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
uid: mvc/models/model-binding
ms.openlocfilehash: d36e42ef2517068ade3f874dc62cc7587ee3ca98
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355669"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="3871c-103">Model binding no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3871c-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3871c-104">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="3871c-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3871c-105">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3871c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3871c-106">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="3871c-106">What is Model binding</span></span>

<span data-ttu-id="3871c-107">Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3871c-108">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3871c-109">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="3871c-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3871c-110">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="3871c-110">Model binding automates this process.</span></span> <span data-ttu-id="3871c-111">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="3871c-111">The model binding system:</span></span>

* <span data-ttu-id="3871c-112">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3871c-113">Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="3871c-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3871c-114">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="3871c-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3871c-115">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="3871c-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3871c-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="3871c-116">Example</span></span>

<span data-ttu-id="3871c-117">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="3871c-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3871c-118">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="3871c-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3871c-119">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="3871c-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3871c-120">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="3871c-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3871c-121">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3871c-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3871c-122">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="3871c-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3871c-123">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3871c-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3871c-124">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3871c-125">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="3871c-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3871c-126">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="3871c-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3871c-127">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3871c-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3871c-128">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3871c-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3871c-129">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="3871c-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3871c-130">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="3871c-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3871c-131">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="3871c-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3871c-132">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="3871c-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3871c-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="3871c-133">Targets</span></span>

<span data-ttu-id="3871c-134">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="3871c-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3871c-135">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3871c-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3871c-136">Os parâmetros do método do manipulador do Razor Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3871c-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3871c-137">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="3871c-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3871c-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="3871c-138">[BindProperty] attribute</span></span>

<span data-ttu-id="3871c-139">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="3871c-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="3871c-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="3871c-140">[BindProperties] attribute</span></span>

<span data-ttu-id="3871c-141">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="3871c-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3871c-142">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="3871c-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3871c-143">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="3871c-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3871c-144">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3871c-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3871c-145">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="3871c-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3871c-146">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3871c-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3871c-147">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3871c-148">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="3871c-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3871c-149">Fontes</span><span class="sxs-lookup"><span data-stu-id="3871c-149">Sources</span></span>

<span data-ttu-id="3871c-150">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="3871c-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3871c-151">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="3871c-151">Form fields</span></span>
1. <span data-ttu-id="3871c-152">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="3871c-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3871c-153">Dados de rota</span><span class="sxs-lookup"><span data-stu-id="3871c-153">Route data</span></span>
1. <span data-ttu-id="3871c-154">Consultar parâmetros de cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="3871c-154">Query string parameters</span></span>
1. <span data-ttu-id="3871c-155">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="3871c-155">Uploaded files</span></span>

<span data-ttu-id="3871c-156">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="3871c-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3871c-157">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="3871c-157">There are a few exceptions:</span></span>

* <span data-ttu-id="3871c-158">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3871c-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3871c-159">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="3871c-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3871c-160">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="3871c-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3871c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3871c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3871c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3871c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3871c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3871c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3871c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3871c-166">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="3871c-166">These attributes:</span></span>

* <span data-ttu-id="3871c-167">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3871c-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3871c-168">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="3871c-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3871c-169">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3871c-170">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3871c-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3871c-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="3871c-171">[FromBody] attribute</span></span>

<span data-ttu-id="3871c-172">Aplique o atributo `[FromBody]` a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3871c-173">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="3871c-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3871c-174">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3871c-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3871c-175">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="3871c-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3871c-176">Por exemplo, a ação a seguir `Create` especifica que seu parâmetro `pet` é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="3871c-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3871c-177">A classe `Pet` especifica que sua propriedade `Breed` é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="3871c-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3871c-178">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="3871c-178">In the preceding example:</span></span>

* <span data-ttu-id="3871c-179">O atributo `[FromQuery]` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="3871c-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3871c-180">A propriedade `Breed` não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3871c-181">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="3871c-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3871c-182">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a propriedade `Breed`.</span><span class="sxs-lookup"><span data-stu-id="3871c-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3871c-183">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="3871c-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3871c-184">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para associar outros parâmetros de `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="3871c-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3871c-185">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="3871c-185">Additional sources</span></span>

<span data-ttu-id="3871c-186">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="3871c-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="3871c-187">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="3871c-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3871c-188">Por exemplo, talvez você queira dados de cookies ou estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="3871c-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="3871c-189">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="3871c-189">To get data from a new source:</span></span>

* <span data-ttu-id="3871c-190">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="3871c-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3871c-191">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="3871c-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3871c-192">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3871c-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3871c-193">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookies.</span><span class="sxs-lookup"><span data-stu-id="3871c-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="3871c-194">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3871c-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="3871c-195">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="3871c-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3871c-196">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="3871c-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3871c-197">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="3871c-197">No source for a model property</span></span>

<span data-ttu-id="3871c-198">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3871c-199">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="3871c-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3871c-200">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="3871c-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3871c-201">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="3871c-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3871c-202">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="3871c-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3871c-203">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="3871c-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3871c-204">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="3871c-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3871c-205">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o atributo [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="3871c-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3871c-206">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3871c-207">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3871c-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3871c-208">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="3871c-208">Type conversion errors</span></span>

<span data-ttu-id="3871c-209">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="3871c-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3871c-210">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="3871c-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3871c-211">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="3871c-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3871c-212">Em uma Razor Page, exiba novamente a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="3871c-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3871c-213">Validação no lado do cliente captura a maioria dos dados inválidos que de outra forma seriam enviados a um formulário do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3871c-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="3871c-214">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="3871c-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3871c-215">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="3871c-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3871c-216">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="3871c-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3871c-217">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="3871c-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3871c-218">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="3871c-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3871c-219">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="3871c-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3871c-220">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="3871c-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3871c-221">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3871c-222">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="3871c-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3871c-223">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="3871c-223">Simple types</span></span>

<span data-ttu-id="3871c-224">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="3871c-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3871c-225">Booliano</span><span class="sxs-lookup"><span data-stu-id="3871c-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3871c-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3871c-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3871c-227">Char</span><span class="sxs-lookup"><span data-stu-id="3871c-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3871c-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="3871c-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3871c-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3871c-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3871c-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="3871c-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3871c-231">Duplo</span><span class="sxs-lookup"><span data-stu-id="3871c-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3871c-232">Enum</span><span class="sxs-lookup"><span data-stu-id="3871c-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3871c-233">Guid</span><span class="sxs-lookup"><span data-stu-id="3871c-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3871c-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3871c-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3871c-235">Simples</span><span class="sxs-lookup"><span data-stu-id="3871c-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3871c-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3871c-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3871c-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3871c-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3871c-238">Uri</span><span class="sxs-lookup"><span data-stu-id="3871c-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3871c-239">Versão</span><span class="sxs-lookup"><span data-stu-id="3871c-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3871c-240">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="3871c-240">Complex types</span></span>

<span data-ttu-id="3871c-241">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="3871c-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3871c-242">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="3871c-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3871c-243">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="3871c-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="3871c-244">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3871c-245">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3871c-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3871c-246">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="3871c-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3871c-247">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3871c-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3871c-248">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="3871c-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3871c-249">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="3871c-249">Prefix = parameter name</span></span>

<span data-ttu-id="3871c-250">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="3871c-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3871c-251">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="3871c-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3871c-252">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3871c-253">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="3871c-253">Prefix = property name</span></span>

<span data-ttu-id="3871c-254">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3871c-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3871c-255">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3871c-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3871c-256">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3871c-257">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="3871c-257">Custom prefix</span></span>

<span data-ttu-id="3871c-258">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="3871c-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3871c-259">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3871c-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3871c-260">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3871c-261">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="3871c-261">Attributes for complex type targets</span></span>

<span data-ttu-id="3871c-262">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="3871c-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="3871c-263">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="3871c-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3871c-264">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="3871c-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3871c-265">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3871c-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="3871c-266">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="3871c-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="3871c-267">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="3871c-267">[BindRequired] attribute</span></span>

<span data-ttu-id="3871c-268">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3871c-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3871c-269">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3871c-270">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="3871c-271">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="3871c-271">[BindNever] attribute</span></span>

<span data-ttu-id="3871c-272">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3871c-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3871c-273">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3871c-274">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="3871c-275">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="3871c-275">[Bind] attribute</span></span>

<span data-ttu-id="3871c-276">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="3871c-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3871c-277">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="3871c-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="3871c-278">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="3871c-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3871c-279">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="3871c-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3871c-280">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="3871c-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="3871c-281">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="3871c-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3871c-282">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="3871c-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3871c-283">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3871c-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="3871c-284">Coleções</span><span class="sxs-lookup"><span data-stu-id="3871c-284">Collections</span></span>

<span data-ttu-id="3871c-285">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3871c-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3871c-286">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3871c-287">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-287">For example:</span></span>

* <span data-ttu-id="3871c-288">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3871c-289">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="3871c-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="3871c-290">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="3871c-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3871c-291">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3871c-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="3871c-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3871c-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="3871c-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3871c-294">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="3871c-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3871c-295">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="3871c-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3871c-296">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="3871c-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3871c-297">Dicionários</span><span class="sxs-lookup"><span data-stu-id="3871c-297">Dictionaries</span></span>

<span data-ttu-id="3871c-298">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3871c-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3871c-299">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3871c-300">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-300">For example:</span></span>

* <span data-ttu-id="3871c-301">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3871c-302">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="3871c-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="3871c-303">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3871c-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3871c-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3871c-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="3871c-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3871c-306">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="3871c-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3871c-307">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="3871c-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3871c-308">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="3871c-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3871c-309">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="3871c-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3871c-310">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="3871c-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3871c-311">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="3871c-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3871c-312">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="3871c-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3871c-313">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3871c-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3871c-314">Copie o código de [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="3871c-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3871c-315">Substitua o [valor de cultura](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="3871c-315">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3871c-316">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="3871c-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3871c-317">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="3871c-317">Special data types</span></span>

<span data-ttu-id="3871c-318">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="3871c-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3871c-319">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="3871c-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3871c-320">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3871c-321">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="3871c-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3871c-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3871c-322">CancellationToken</span></span>

<span data-ttu-id="3871c-323">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="3871c-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3871c-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="3871c-324">FormCollection</span></span>

<span data-ttu-id="3871c-325">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3871c-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3871c-326">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="3871c-326">Input formatters</span></span>

<span data-ttu-id="3871c-327">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="3871c-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3871c-328">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3871c-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3871c-329">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="3871c-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3871c-330">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3871c-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3871c-331">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="3871c-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3871c-332">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="3871c-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3871c-333">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="3871c-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3871c-334">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="3871c-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3871c-335">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="3871c-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="3871c-336">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3871c-337">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3871c-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="3871c-338">Personalizar a associação de modelo com formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="3871c-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="3871c-339">Um formatador de entrada assume total responsabilidade pela leitura de dados do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="3871c-340">Para personalizar esse processo, configure as APIs usadas pelo formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="3871c-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="3871c-341">Esta seção descreve como personalizar o formatador de entrada baseado em `System.Text.Json`para entender um tipo personalizado chamado `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="3871c-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="3871c-342">Considere o modelo a seguir, que contém uma propriedade de `ObjectId` personalizada chamada `Id`:</span><span class="sxs-lookup"><span data-stu-id="3871c-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="3871c-343">Para personalizar o processo de associação de modelo ao usar `System.Text.Json`, crie uma classe derivada de <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="3871c-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="3871c-344">Para usar um conversor personalizado, aplique o atributo <xref:System.Text.Json.Serialization.JsonConverterAttribute> ao tipo.</span><span class="sxs-lookup"><span data-stu-id="3871c-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="3871c-345">No exemplo a seguir, o tipo de `ObjectId` é configurado com `ObjectIdConverter` como seu conversor personalizado:</span><span class="sxs-lookup"><span data-stu-id="3871c-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="3871c-346">Para obter mais informações, consulte [como escrever conversores personalizados](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="3871c-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3871c-347">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="3871c-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="3871c-348">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="3871c-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3871c-349">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="3871c-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3871c-350">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="3871c-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3871c-351">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3871c-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3871c-352">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="3871c-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="3871c-353">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3871c-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3871c-354">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="3871c-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="3871c-355">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="3871c-355">Custom model binders</span></span>

<span data-ttu-id="3871c-356">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="3871c-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3871c-357">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="3871c-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3871c-358">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="3871c-358">Manual model binding</span></span> 

<span data-ttu-id="3871c-359">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3871c-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3871c-360">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="3871c-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3871c-361">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="3871c-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3871c-362">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="3871c-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3871c-363">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="3871c-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> usa provedores de valor para obter dados do corpo do formulário, Cadeia de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3871c-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="3871c-365">`TryUpdateModelAsync` normalmente é:</span><span class="sxs-lookup"><span data-stu-id="3871c-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="3871c-366">Usado com os aplicativos Razor Pages e MVC usando controladores e exibições para evitar o excesso de postagens.</span><span class="sxs-lookup"><span data-stu-id="3871c-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="3871c-367">Não usado com uma API da Web, a menos que consumido de dados de formulário, cadeias de caracteres de consulta e dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3871c-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="3871c-368">Os pontos de extremidade da API Web que consomem JSON usam [formatadores de entrada](#input-formatters) para desserializar o corpo da solicitação em um objeto.</span><span class="sxs-lookup"><span data-stu-id="3871c-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="3871c-369">Para obter mais informações, consulte [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="3871c-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="3871c-370">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="3871c-370">[FromServices] attribute</span></span>

<span data-ttu-id="3871c-371">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="3871c-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3871c-372">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="3871c-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3871c-373">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3871c-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3871c-374">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="3871c-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3871c-375">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3871c-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3871c-376">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="3871c-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3871c-377">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3871c-377">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3871c-378">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="3871c-378">What is Model binding</span></span>

<span data-ttu-id="3871c-379">Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3871c-380">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3871c-381">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="3871c-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3871c-382">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="3871c-382">Model binding automates this process.</span></span> <span data-ttu-id="3871c-383">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="3871c-383">The model binding system:</span></span>

* <span data-ttu-id="3871c-384">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3871c-385">Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="3871c-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3871c-386">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="3871c-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3871c-387">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="3871c-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3871c-388">Exemplo</span><span class="sxs-lookup"><span data-stu-id="3871c-388">Example</span></span>

<span data-ttu-id="3871c-389">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="3871c-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3871c-390">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="3871c-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3871c-391">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="3871c-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3871c-392">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="3871c-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3871c-393">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3871c-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3871c-394">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="3871c-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3871c-395">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3871c-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3871c-396">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3871c-397">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="3871c-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3871c-398">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="3871c-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3871c-399">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3871c-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3871c-400">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3871c-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3871c-401">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="3871c-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3871c-402">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="3871c-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3871c-403">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="3871c-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3871c-404">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="3871c-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3871c-405">Destinos</span><span class="sxs-lookup"><span data-stu-id="3871c-405">Targets</span></span>

<span data-ttu-id="3871c-406">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="3871c-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3871c-407">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3871c-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3871c-408">Os parâmetros do método do manipulador do Razor Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3871c-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3871c-409">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="3871c-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3871c-410">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="3871c-410">[BindProperty] attribute</span></span>

<span data-ttu-id="3871c-411">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="3871c-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="3871c-412">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="3871c-412">[BindProperties] attribute</span></span>

<span data-ttu-id="3871c-413">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="3871c-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3871c-414">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="3871c-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3871c-415">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="3871c-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3871c-416">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3871c-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3871c-417">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="3871c-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3871c-418">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3871c-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3871c-419">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3871c-420">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="3871c-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3871c-421">Fontes</span><span class="sxs-lookup"><span data-stu-id="3871c-421">Sources</span></span>

<span data-ttu-id="3871c-422">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="3871c-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3871c-423">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="3871c-423">Form fields</span></span>
1. <span data-ttu-id="3871c-424">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="3871c-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3871c-425">Dados de rota</span><span class="sxs-lookup"><span data-stu-id="3871c-425">Route data</span></span>
1. <span data-ttu-id="3871c-426">Consultar parâmetros de cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="3871c-426">Query string parameters</span></span>
1. <span data-ttu-id="3871c-427">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="3871c-427">Uploaded files</span></span>

<span data-ttu-id="3871c-428">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="3871c-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3871c-429">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="3871c-429">There are a few exceptions:</span></span>

* <span data-ttu-id="3871c-430">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3871c-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3871c-431">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="3871c-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3871c-432">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="3871c-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3871c-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3871c-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3871c-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3871c-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3871c-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3871c-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3871c-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3871c-438">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="3871c-438">These attributes:</span></span>

* <span data-ttu-id="3871c-439">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3871c-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3871c-440">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="3871c-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3871c-441">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3871c-442">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3871c-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3871c-443">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="3871c-443">[FromBody] attribute</span></span>

<span data-ttu-id="3871c-444">Aplique o atributo `[FromBody]` a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3871c-445">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="3871c-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3871c-446">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3871c-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3871c-447">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="3871c-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3871c-448">Por exemplo, a ação a seguir `Create` especifica que seu parâmetro `pet` é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="3871c-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3871c-449">A classe `Pet` especifica que sua propriedade `Breed` é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="3871c-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3871c-450">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="3871c-450">In the preceding example:</span></span>

* <span data-ttu-id="3871c-451">O atributo `[FromQuery]` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="3871c-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3871c-452">A propriedade `Breed` não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3871c-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3871c-453">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="3871c-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3871c-454">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a propriedade `Breed`.</span><span class="sxs-lookup"><span data-stu-id="3871c-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3871c-455">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="3871c-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3871c-456">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para associar outros parâmetros de `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="3871c-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3871c-457">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="3871c-457">Additional sources</span></span>

<span data-ttu-id="3871c-458">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="3871c-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="3871c-459">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="3871c-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3871c-460">Por exemplo, talvez você queira dados de cookies ou estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="3871c-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="3871c-461">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="3871c-461">To get data from a new source:</span></span>

* <span data-ttu-id="3871c-462">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="3871c-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3871c-463">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="3871c-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3871c-464">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3871c-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3871c-465">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookies.</span><span class="sxs-lookup"><span data-stu-id="3871c-465">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="3871c-466">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3871c-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="3871c-467">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="3871c-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3871c-468">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="3871c-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3871c-469">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="3871c-469">No source for a model property</span></span>

<span data-ttu-id="3871c-470">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3871c-471">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="3871c-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3871c-472">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="3871c-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3871c-473">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="3871c-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3871c-474">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="3871c-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3871c-475">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="3871c-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3871c-476">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="3871c-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3871c-477">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o atributo [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="3871c-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3871c-478">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3871c-479">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3871c-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3871c-480">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="3871c-480">Type conversion errors</span></span>

<span data-ttu-id="3871c-481">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="3871c-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3871c-482">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="3871c-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3871c-483">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="3871c-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3871c-484">Em uma Razor Page, exiba novamente a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="3871c-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3871c-485">Validação no lado do cliente captura a maioria dos dados inválidos que de outra forma seriam enviados a um formulário do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3871c-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="3871c-486">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="3871c-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3871c-487">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="3871c-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3871c-488">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="3871c-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3871c-489">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="3871c-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3871c-490">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="3871c-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3871c-491">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="3871c-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3871c-492">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="3871c-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3871c-493">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3871c-494">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="3871c-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3871c-495">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="3871c-495">Simple types</span></span>

<span data-ttu-id="3871c-496">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="3871c-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3871c-497">Booliano</span><span class="sxs-lookup"><span data-stu-id="3871c-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3871c-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3871c-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3871c-499">Char</span><span class="sxs-lookup"><span data-stu-id="3871c-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3871c-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="3871c-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3871c-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3871c-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3871c-502">Decimal</span><span class="sxs-lookup"><span data-stu-id="3871c-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3871c-503">Duplo</span><span class="sxs-lookup"><span data-stu-id="3871c-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3871c-504">Enum</span><span class="sxs-lookup"><span data-stu-id="3871c-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3871c-505">Guid</span><span class="sxs-lookup"><span data-stu-id="3871c-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3871c-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3871c-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3871c-507">Simples</span><span class="sxs-lookup"><span data-stu-id="3871c-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3871c-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3871c-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3871c-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3871c-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3871c-510">Uri</span><span class="sxs-lookup"><span data-stu-id="3871c-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3871c-511">Versão</span><span class="sxs-lookup"><span data-stu-id="3871c-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3871c-512">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="3871c-512">Complex types</span></span>

<span data-ttu-id="3871c-513">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="3871c-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3871c-514">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="3871c-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3871c-515">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="3871c-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="3871c-516">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3871c-517">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3871c-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3871c-518">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="3871c-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3871c-519">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3871c-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3871c-520">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="3871c-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3871c-521">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="3871c-521">Prefix = parameter name</span></span>

<span data-ttu-id="3871c-522">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="3871c-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3871c-523">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="3871c-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3871c-524">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3871c-525">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="3871c-525">Prefix = property name</span></span>

<span data-ttu-id="3871c-526">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3871c-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3871c-527">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3871c-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3871c-528">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3871c-529">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="3871c-529">Custom prefix</span></span>

<span data-ttu-id="3871c-530">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="3871c-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3871c-531">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3871c-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3871c-532">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3871c-533">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="3871c-533">Attributes for complex type targets</span></span>

<span data-ttu-id="3871c-534">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="3871c-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="3871c-535">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="3871c-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3871c-536">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="3871c-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3871c-537">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3871c-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="3871c-538">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="3871c-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="3871c-539">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="3871c-539">[BindRequired] attribute</span></span>

<span data-ttu-id="3871c-540">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3871c-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3871c-541">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3871c-542">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="3871c-543">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="3871c-543">[BindNever] attribute</span></span>

<span data-ttu-id="3871c-544">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3871c-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3871c-545">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3871c-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3871c-546">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="3871c-547">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="3871c-547">[Bind] attribute</span></span>

<span data-ttu-id="3871c-548">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="3871c-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3871c-549">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="3871c-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="3871c-550">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="3871c-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3871c-551">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="3871c-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3871c-552">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="3871c-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="3871c-553">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="3871c-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3871c-554">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="3871c-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3871c-555">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3871c-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="3871c-556">Coleções</span><span class="sxs-lookup"><span data-stu-id="3871c-556">Collections</span></span>

<span data-ttu-id="3871c-557">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3871c-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3871c-558">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3871c-559">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-559">For example:</span></span>

* <span data-ttu-id="3871c-560">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3871c-561">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="3871c-561">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="3871c-562">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="3871c-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3871c-563">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3871c-564">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="3871c-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3871c-565">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="3871c-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3871c-566">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="3871c-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3871c-567">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="3871c-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3871c-568">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="3871c-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3871c-569">Dicionários</span><span class="sxs-lookup"><span data-stu-id="3871c-569">Dictionaries</span></span>

<span data-ttu-id="3871c-570">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3871c-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3871c-571">Se nenhuma correspondência for encontrada, procurará um dos formatos compatíveis sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3871c-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3871c-572">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-572">For example:</span></span>

* <span data-ttu-id="3871c-573">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3871c-574">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="3871c-574">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="3871c-575">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3871c-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3871c-576">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3871c-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3871c-577">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="3871c-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3871c-578">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="3871c-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3871c-579">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="3871c-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3871c-580">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="3871c-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3871c-581">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="3871c-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3871c-582">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="3871c-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3871c-583">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="3871c-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3871c-584">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="3871c-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3871c-585">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3871c-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3871c-586">Copie o código de [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="3871c-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3871c-587">Substitua o [valor de cultura](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="3871c-587">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3871c-588">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="3871c-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3871c-589">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="3871c-589">Special data types</span></span>

<span data-ttu-id="3871c-590">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="3871c-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3871c-591">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="3871c-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3871c-592">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3871c-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3871c-593">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="3871c-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3871c-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3871c-594">CancellationToken</span></span>

<span data-ttu-id="3871c-595">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="3871c-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3871c-596">FormCollection</span><span class="sxs-lookup"><span data-stu-id="3871c-596">FormCollection</span></span>

<span data-ttu-id="3871c-597">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3871c-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3871c-598">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="3871c-598">Input formatters</span></span>

<span data-ttu-id="3871c-599">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="3871c-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3871c-600">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3871c-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3871c-601">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="3871c-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3871c-602">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3871c-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3871c-603">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="3871c-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3871c-604">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="3871c-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3871c-605">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="3871c-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3871c-606">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="3871c-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3871c-607">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="3871c-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="3871c-608">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3871c-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3871c-609">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3871c-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3871c-610">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="3871c-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="3871c-611">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="3871c-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3871c-612">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="3871c-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3871c-613">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="3871c-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3871c-614">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3871c-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3871c-615">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="3871c-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="3871c-616">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3871c-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3871c-617">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="3871c-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="3871c-618">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="3871c-618">Custom model binders</span></span>

<span data-ttu-id="3871c-619">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="3871c-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3871c-620">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="3871c-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3871c-621">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="3871c-621">Manual model binding</span></span>

<span data-ttu-id="3871c-622">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3871c-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3871c-623">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="3871c-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3871c-624">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="3871c-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3871c-625">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="3871c-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3871c-626">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3871c-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="3871c-627">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="3871c-627">[FromServices] attribute</span></span>

<span data-ttu-id="3871c-628">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="3871c-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3871c-629">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="3871c-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3871c-630">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3871c-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3871c-631">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="3871c-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3871c-632">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3871c-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
