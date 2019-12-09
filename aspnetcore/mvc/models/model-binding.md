---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/21/2019
uid: mvc/models/model-binding
ms.openlocfilehash: da6cc25e0bbb1b2301529b34eab4c91f9ccb46eb
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944289"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="3b46b-103">Model binding no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b46b-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b46b-104">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="3b46b-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3b46b-105">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3b46b-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3b46b-106">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="3b46b-106">What is Model binding</span></span>

<span data-ttu-id="3b46b-107">Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3b46b-108">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3b46b-109">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3b46b-110">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-110">Model binding automates this process.</span></span> <span data-ttu-id="3b46b-111">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="3b46b-111">The model binding system:</span></span>

* <span data-ttu-id="3b46b-112">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3b46b-113">Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="3b46b-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3b46b-114">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="3b46b-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3b46b-115">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3b46b-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="3b46b-116">Example</span></span>

<span data-ttu-id="3b46b-117">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="3b46b-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3b46b-118">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="3b46b-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3b46b-119">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="3b46b-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3b46b-120">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3b46b-121">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3b46b-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3b46b-122">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="3b46b-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3b46b-123">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3b46b-124">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3b46b-125">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="3b46b-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3b46b-126">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3b46b-127">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3b46b-128">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3b46b-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3b46b-129">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3b46b-130">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="3b46b-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3b46b-131">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="3b46b-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3b46b-132">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="3b46b-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3b46b-133">Destinos</span><span class="sxs-lookup"><span data-stu-id="3b46b-133">Targets</span></span>

<span data-ttu-id="3b46b-134">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3b46b-135">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3b46b-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3b46b-136">Os parâmetros do método do manipulador do Razor Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3b46b-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3b46b-137">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3b46b-138">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="3b46b-138">[BindProperty] attribute</span></span>

<span data-ttu-id="3b46b-139">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="3b46b-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="3b46b-140">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="3b46b-140">[BindProperties] attribute</span></span>

<span data-ttu-id="3b46b-141">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="3b46b-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3b46b-142">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="3b46b-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3b46b-143">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="3b46b-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3b46b-144">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3b46b-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3b46b-145">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3b46b-146">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3b46b-147">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3b46b-148">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3b46b-149">Fontes</span><span class="sxs-lookup"><span data-stu-id="3b46b-149">Sources</span></span>

<span data-ttu-id="3b46b-150">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="3b46b-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3b46b-151">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="3b46b-151">Form fields</span></span>
1. <span data-ttu-id="3b46b-152">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="3b46b-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3b46b-153">Dados de rota</span><span class="sxs-lookup"><span data-stu-id="3b46b-153">Route data</span></span>
1. <span data-ttu-id="3b46b-154">Consultar parâmetros de cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="3b46b-154">Query string parameters</span></span>
1. <span data-ttu-id="3b46b-155">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="3b46b-155">Uploaded files</span></span>

<span data-ttu-id="3b46b-156">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="3b46b-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3b46b-157">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="3b46b-157">There are a few exceptions:</span></span>

* <span data-ttu-id="3b46b-158">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3b46b-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3b46b-159">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3b46b-160">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="3b46b-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3b46b-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3b46b-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3b46b-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3b46b-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3b46b-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3b46b-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3b46b-166">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-166">These attributes:</span></span>

* <span data-ttu-id="3b46b-167">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3b46b-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3b46b-168">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="3b46b-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3b46b-169">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3b46b-170">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3b46b-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3b46b-171">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="3b46b-171">[FromBody] attribute</span></span>

<span data-ttu-id="3b46b-172">Aplique o atributo `[FromBody]` a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3b46b-173">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="3b46b-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3b46b-174">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3b46b-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3b46b-175">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3b46b-176">Por exemplo, a ação a seguir `Create` especifica que seu parâmetro `pet` é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3b46b-177">A classe `Pet` especifica que sua propriedade `Breed` é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="3b46b-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3b46b-178">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="3b46b-178">In the preceding example:</span></span>

* <span data-ttu-id="3b46b-179">O atributo `[FromQuery]` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3b46b-180">A propriedade `Breed` não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3b46b-181">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3b46b-182">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a propriedade `Breed`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3b46b-183">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3b46b-184">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para associar outros parâmetros de `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3b46b-185">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="3b46b-185">Additional sources</span></span>

<span data-ttu-id="3b46b-186">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="3b46b-187">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="3b46b-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3b46b-188">Por exemplo, talvez você queira dados de cookies ou estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="3b46b-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="3b46b-189">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="3b46b-189">To get data from a new source:</span></span>

* <span data-ttu-id="3b46b-190">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3b46b-191">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3b46b-192">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3b46b-193">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookies.</span><span class="sxs-lookup"><span data-stu-id="3b46b-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="3b46b-194">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="3b46b-195">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3b46b-196">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3b46b-197">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="3b46b-197">No source for a model property</span></span>

<span data-ttu-id="3b46b-198">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3b46b-199">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="3b46b-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3b46b-200">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3b46b-201">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3b46b-202">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="3b46b-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3b46b-203">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="3b46b-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3b46b-204">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3b46b-205">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o atributo [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="3b46b-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3b46b-206">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3b46b-207">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3b46b-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3b46b-208">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="3b46b-208">Type conversion errors</span></span>

<span data-ttu-id="3b46b-209">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="3b46b-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3b46b-210">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="3b46b-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3b46b-211">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="3b46b-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3b46b-212">Em uma Razor Page, exiba novamente a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="3b46b-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3b46b-213">Validação no lado do cliente captura a maioria dos dados inválidos que de outra forma seriam enviados a um formulário do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3b46b-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="3b46b-214">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="3b46b-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3b46b-215">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="3b46b-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3b46b-216">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3b46b-217">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="3b46b-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3b46b-218">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="3b46b-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3b46b-219">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3b46b-220">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="3b46b-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3b46b-221">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3b46b-222">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="3b46b-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3b46b-223">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="3b46b-223">Simple types</span></span>

<span data-ttu-id="3b46b-224">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="3b46b-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3b46b-225">Booliano</span><span class="sxs-lookup"><span data-stu-id="3b46b-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3b46b-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3b46b-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3b46b-227">Char</span><span class="sxs-lookup"><span data-stu-id="3b46b-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3b46b-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="3b46b-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3b46b-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3b46b-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3b46b-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="3b46b-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3b46b-231">Duplo</span><span class="sxs-lookup"><span data-stu-id="3b46b-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3b46b-232">Enum</span><span class="sxs-lookup"><span data-stu-id="3b46b-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3b46b-233">Guid</span><span class="sxs-lookup"><span data-stu-id="3b46b-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3b46b-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3b46b-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3b46b-235">Simples</span><span class="sxs-lookup"><span data-stu-id="3b46b-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3b46b-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3b46b-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3b46b-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3b46b-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3b46b-238">Uri</span><span class="sxs-lookup"><span data-stu-id="3b46b-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3b46b-239">Versão</span><span class="sxs-lookup"><span data-stu-id="3b46b-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3b46b-240">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="3b46b-240">Complex types</span></span>

<span data-ttu-id="3b46b-241">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="3b46b-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3b46b-242">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="3b46b-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3b46b-243">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="3b46b-244">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3b46b-245">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3b46b-246">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="3b46b-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3b46b-247">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3b46b-248">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3b46b-249">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="3b46b-249">Prefix = parameter name</span></span>

<span data-ttu-id="3b46b-250">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3b46b-251">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3b46b-252">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3b46b-253">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="3b46b-253">Prefix = property name</span></span>

<span data-ttu-id="3b46b-254">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3b46b-255">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3b46b-256">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3b46b-257">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="3b46b-257">Custom prefix</span></span>

<span data-ttu-id="3b46b-258">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3b46b-259">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3b46b-260">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3b46b-261">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="3b46b-261">Attributes for complex type targets</span></span>

<span data-ttu-id="3b46b-262">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="3b46b-263">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="3b46b-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3b46b-264">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3b46b-265">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3b46b-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="3b46b-266">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="3b46b-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="3b46b-267">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="3b46b-267">[BindRequired] attribute</span></span>

<span data-ttu-id="3b46b-268">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3b46b-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3b46b-269">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3b46b-270">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="3b46b-271">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="3b46b-271">[BindNever] attribute</span></span>

<span data-ttu-id="3b46b-272">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3b46b-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3b46b-273">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3b46b-274">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="3b46b-275">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="3b46b-275">[Bind] attribute</span></span>

<span data-ttu-id="3b46b-276">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="3b46b-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3b46b-277">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="3b46b-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="3b46b-278">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="3b46b-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3b46b-279">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="3b46b-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3b46b-280">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="3b46b-281">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="3b46b-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3b46b-282">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3b46b-283">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3b46b-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="3b46b-284">Coleções</span><span class="sxs-lookup"><span data-stu-id="3b46b-284">Collections</span></span>

<span data-ttu-id="3b46b-285">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3b46b-286">Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3b46b-287">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-287">For example:</span></span>

* <span data-ttu-id="3b46b-288">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3b46b-289">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="3b46b-290">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="3b46b-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3b46b-291">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3b46b-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="3b46b-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3b46b-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="3b46b-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3b46b-294">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="3b46b-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3b46b-295">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3b46b-296">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3b46b-297">Dicionários</span><span class="sxs-lookup"><span data-stu-id="3b46b-297">Dictionaries</span></span>

<span data-ttu-id="3b46b-298">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3b46b-299">Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3b46b-300">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-300">For example:</span></span>

* <span data-ttu-id="3b46b-301">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3b46b-302">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="3b46b-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="3b46b-303">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3b46b-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3b46b-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3b46b-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="3b46b-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3b46b-306">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="3b46b-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3b46b-307">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="3b46b-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3b46b-308">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="3b46b-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3b46b-309">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="3b46b-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3b46b-310">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="3b46b-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3b46b-311">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="3b46b-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3b46b-312">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="3b46b-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3b46b-313">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3b46b-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3b46b-314">Copie o código de [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="3b46b-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3b46b-315">Substitua o [valor de cultura](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="3b46b-315">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3b46b-316">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3b46b-317">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="3b46b-317">Special data types</span></span>

<span data-ttu-id="3b46b-318">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="3b46b-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3b46b-319">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="3b46b-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3b46b-320">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3b46b-321">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3b46b-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3b46b-322">CancellationToken</span></span>

<span data-ttu-id="3b46b-323">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3b46b-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="3b46b-324">FormCollection</span></span>

<span data-ttu-id="3b46b-325">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3b46b-326">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="3b46b-326">Input formatters</span></span>

<span data-ttu-id="3b46b-327">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="3b46b-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3b46b-328">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3b46b-329">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="3b46b-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3b46b-330">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3b46b-331">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="3b46b-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3b46b-332">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="3b46b-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3b46b-333">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3b46b-334">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3b46b-335">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="3b46b-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="3b46b-336">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3b46b-337">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3b46b-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3b46b-338">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="3b46b-338">Exclude specified types from model binding</span></span>

<span data-ttu-id="3b46b-339">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="3b46b-339">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3b46b-340">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="3b46b-340">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3b46b-341">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-341">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3b46b-342">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-342">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3b46b-343">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-343">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="3b46b-344">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-344">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3b46b-345">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-345">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="3b46b-346">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="3b46b-346">Custom model binders</span></span>

<span data-ttu-id="3b46b-347">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="3b46b-347">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3b46b-348">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="3b46b-348">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3b46b-349">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="3b46b-349">Manual model binding</span></span>

<span data-ttu-id="3b46b-350">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3b46b-350">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3b46b-351">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-351">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3b46b-352">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-352">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3b46b-353">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="3b46b-353">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3b46b-354">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-354">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="3b46b-355">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="3b46b-355">[FromServices] attribute</span></span>

<span data-ttu-id="3b46b-356">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-356">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3b46b-357">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="3b46b-357">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3b46b-358">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3b46b-358">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3b46b-359">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-359">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b46b-360">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3b46b-360">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3b46b-361">Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.</span><span class="sxs-lookup"><span data-stu-id="3b46b-361">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3b46b-362">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3b46b-362">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3b46b-363">O que é o model binding</span><span class="sxs-lookup"><span data-stu-id="3b46b-363">What is Model binding</span></span>

<span data-ttu-id="3b46b-364">Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-364">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3b46b-365">Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-365">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3b46b-366">Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-366">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3b46b-367">O model binding automatiza esse processo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-367">Model binding automates this process.</span></span> <span data-ttu-id="3b46b-368">O sistema de model binding:</span><span class="sxs-lookup"><span data-stu-id="3b46b-368">The model binding system:</span></span>

* <span data-ttu-id="3b46b-369">Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-369">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3b46b-370">Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.</span><span class="sxs-lookup"><span data-stu-id="3b46b-370">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3b46b-371">Converte dados de cadeia de caracteres em tipos .NET.</span><span class="sxs-lookup"><span data-stu-id="3b46b-371">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3b46b-372">Atualiza as propriedades de tipos complexos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-372">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3b46b-373">Exemplo</span><span class="sxs-lookup"><span data-stu-id="3b46b-373">Example</span></span>

<span data-ttu-id="3b46b-374">Suponha que você tenha o seguinte método de ação:</span><span class="sxs-lookup"><span data-stu-id="3b46b-374">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3b46b-375">E o aplicativo receba uma solicitação com esta URL:</span><span class="sxs-lookup"><span data-stu-id="3b46b-375">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3b46b-376">A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:</span><span class="sxs-lookup"><span data-stu-id="3b46b-376">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3b46b-377">Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-377">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3b46b-378">Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3b46b-378">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3b46b-379">Converte a cadeia de caracteres "2" em inteiro 2.</span><span class="sxs-lookup"><span data-stu-id="3b46b-379">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3b46b-380">Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-380">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3b46b-381">Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-381">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3b46b-382">A correspondência de nomes não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="3b46b-382">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3b46b-383">Converte a cadeia de caracteres "true" no booliano `true`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-383">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3b46b-384">A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-384">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3b46b-385">No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3b46b-385">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3b46b-386">Destinos também podem ser as propriedades de um tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-386">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3b46b-387">Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="3b46b-387">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3b46b-388">O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="3b46b-388">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3b46b-389">Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="3b46b-389">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3b46b-390">Destinos</span><span class="sxs-lookup"><span data-stu-id="3b46b-390">Targets</span></span>

<span data-ttu-id="3b46b-391">O model binding tenta encontrar valores para os seguintes tipos de destinos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-391">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3b46b-392">Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3b46b-392">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3b46b-393">Os parâmetros do método do manipulador do Razor Pages para o qual uma solicitação é roteada.</span><span class="sxs-lookup"><span data-stu-id="3b46b-393">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3b46b-394">Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-394">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3b46b-395">Atributo [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="3b46b-395">[BindProperty] attribute</span></span>

<span data-ttu-id="3b46b-396">Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:</span><span class="sxs-lookup"><span data-stu-id="3b46b-396">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="3b46b-397">Atributo [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="3b46b-397">[BindProperties] attribute</span></span>

<span data-ttu-id="3b46b-398">Disponível no ASP.NET Core 2.1 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="3b46b-398">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3b46b-399">Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:</span><span class="sxs-lookup"><span data-stu-id="3b46b-399">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3b46b-400">Model binding para solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="3b46b-400">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3b46b-401">Por padrão, as propriedades não são vinculadas para solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3b46b-401">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3b46b-402">Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-402">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3b46b-403">A ID do registro é usada para pesquisar o item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-403">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3b46b-404">Portanto, não é necessário associar uma propriedade que contém uma instância do modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-404">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3b46b-405">Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-405">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3b46b-406">Fontes</span><span class="sxs-lookup"><span data-stu-id="3b46b-406">Sources</span></span>

<span data-ttu-id="3b46b-407">Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:</span><span class="sxs-lookup"><span data-stu-id="3b46b-407">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3b46b-408">Campos de formulário</span><span class="sxs-lookup"><span data-stu-id="3b46b-408">Form fields</span></span>
1. <span data-ttu-id="3b46b-409">O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).</span><span class="sxs-lookup"><span data-stu-id="3b46b-409">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3b46b-410">Dados de rota</span><span class="sxs-lookup"><span data-stu-id="3b46b-410">Route data</span></span>
1. <span data-ttu-id="3b46b-411">Consultar parâmetros de cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="3b46b-411">Query string parameters</span></span>
1. <span data-ttu-id="3b46b-412">Arquivos carregados</span><span class="sxs-lookup"><span data-stu-id="3b46b-412">Uploaded files</span></span>

<span data-ttu-id="3b46b-413">Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior.</span><span class="sxs-lookup"><span data-stu-id="3b46b-413">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3b46b-414">Há algumas exceções:</span><span class="sxs-lookup"><span data-stu-id="3b46b-414">There are a few exceptions:</span></span>

* <span data-ttu-id="3b46b-415">Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.</span><span class="sxs-lookup"><span data-stu-id="3b46b-415">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3b46b-416">Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-416">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3b46b-417">Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:</span><span class="sxs-lookup"><span data-stu-id="3b46b-417">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3b46b-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtém valores da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3b46b-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtém valores de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="3b46b-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3b46b-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtém valores dos campos de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3b46b-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -obtém valores do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3b46b-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtém valores de cabeçalhos HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3b46b-423">Esses atributos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-423">These attributes:</span></span>

* <span data-ttu-id="3b46b-424">São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3b46b-424">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3b46b-425">Opcionalmente, aceite um valor de nome de modelo no construtor.</span><span class="sxs-lookup"><span data-stu-id="3b46b-425">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3b46b-426">Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-426">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3b46b-427">Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3b46b-427">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3b46b-428">Atributo [FromBody]</span><span class="sxs-lookup"><span data-stu-id="3b46b-428">[FromBody] attribute</span></span>

<span data-ttu-id="3b46b-429">Aplique o atributo `[FromBody]` a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-429">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3b46b-430">O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada.</span><span class="sxs-lookup"><span data-stu-id="3b46b-430">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3b46b-431">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3b46b-431">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3b46b-432">Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-432">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3b46b-433">Por exemplo, a ação a seguir `Create` especifica que seu parâmetro `pet` é populado a partir do corpo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-433">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3b46b-434">A classe `Pet` especifica que sua propriedade `Breed` é populada a partir de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="3b46b-434">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3b46b-435">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="3b46b-435">In the preceding example:</span></span>

* <span data-ttu-id="3b46b-436">O atributo `[FromQuery]` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-436">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3b46b-437">A propriedade `Breed` não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="3b46b-437">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3b46b-438">Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-438">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3b46b-439">Se um valor adequado for encontrado no corpo, esse valor será usado para popular a propriedade `Breed`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-439">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3b46b-440">Não aplique `[FromBody]` a mais de um parâmetro por método de ação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-440">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3b46b-441">Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para associar outros parâmetros de `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-441">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3b46b-442">Fontes adicionais</span><span class="sxs-lookup"><span data-stu-id="3b46b-442">Additional sources</span></span>

<span data-ttu-id="3b46b-443">Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-443">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="3b46b-444">Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="3b46b-444">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3b46b-445">Por exemplo, talvez você queira dados de cookies ou estado de sessão.</span><span class="sxs-lookup"><span data-stu-id="3b46b-445">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="3b46b-446">Para obter dados de uma nova fonte:</span><span class="sxs-lookup"><span data-stu-id="3b46b-446">To get data from a new source:</span></span>

* <span data-ttu-id="3b46b-447">Crie uma classe que implementa `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-447">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3b46b-448">Crie uma classe que implementa `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-448">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3b46b-449">Registre a classe de alocador em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-449">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3b46b-450">O aplicativo de exemplo inclui um [provedor de valor](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookies.</span><span class="sxs-lookup"><span data-stu-id="3b46b-450">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="3b46b-451">Aqui está o código de registro em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-451">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="3b46b-452">O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-452">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3b46b-453">Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-453">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3b46b-454">Nenhuma fonte para uma propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="3b46b-454">No source for a model property</span></span>

<span data-ttu-id="3b46b-455">Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-455">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3b46b-456">A propriedade é definida como null ou um valor padrão:</span><span class="sxs-lookup"><span data-stu-id="3b46b-456">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3b46b-457">Tipos simples anuláveis definidos como `null`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-457">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3b46b-458">Tipos de valor não anuláveis são definidos como `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-458">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3b46b-459">Por exemplo, um parâmetro `int id` é definido como 0.</span><span class="sxs-lookup"><span data-stu-id="3b46b-459">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3b46b-460">Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="3b46b-460">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3b46b-461">As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-461">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3b46b-462">Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o atributo [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="3b46b-462">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3b46b-463">Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-463">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3b46b-464">Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3b46b-464">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3b46b-465">Erros de conversão de tipos</span><span class="sxs-lookup"><span data-stu-id="3b46b-465">Type conversion errors</span></span>

<span data-ttu-id="3b46b-466">Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido.</span><span class="sxs-lookup"><span data-stu-id="3b46b-466">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3b46b-467">O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.</span><span class="sxs-lookup"><span data-stu-id="3b46b-467">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3b46b-468">Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.</span><span class="sxs-lookup"><span data-stu-id="3b46b-468">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3b46b-469">Em uma Razor Page, exiba novamente a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="3b46b-469">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3b46b-470">Validação no lado do cliente captura a maioria dos dados inválidos que de outra forma seriam enviados a um formulário do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3b46b-470">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="3b46b-471">Essa validação torna difícil disparar o código realçado anterior.</span><span class="sxs-lookup"><span data-stu-id="3b46b-471">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3b46b-472">O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="3b46b-472">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3b46b-473">Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-473">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3b46b-474">Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário.</span><span class="sxs-lookup"><span data-stu-id="3b46b-474">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3b46b-475">Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="3b46b-475">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3b46b-476">A entrada inválida aparece em uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-476">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3b46b-477">Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.</span><span class="sxs-lookup"><span data-stu-id="3b46b-477">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3b46b-478">A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-478">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3b46b-479">Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="3b46b-479">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3b46b-480">Tipos simples</span><span class="sxs-lookup"><span data-stu-id="3b46b-480">Simple types</span></span>

<span data-ttu-id="3b46b-481">Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="3b46b-481">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3b46b-482">Booliano</span><span class="sxs-lookup"><span data-stu-id="3b46b-482">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3b46b-483">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3b46b-483">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3b46b-484">Char</span><span class="sxs-lookup"><span data-stu-id="3b46b-484">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3b46b-485">DateTime</span><span class="sxs-lookup"><span data-stu-id="3b46b-485">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3b46b-486">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3b46b-486">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3b46b-487">Decimal</span><span class="sxs-lookup"><span data-stu-id="3b46b-487">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3b46b-488">Duplo</span><span class="sxs-lookup"><span data-stu-id="3b46b-488">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3b46b-489">Enum</span><span class="sxs-lookup"><span data-stu-id="3b46b-489">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3b46b-490">Guid</span><span class="sxs-lookup"><span data-stu-id="3b46b-490">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3b46b-491">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3b46b-491">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3b46b-492">Simples</span><span class="sxs-lookup"><span data-stu-id="3b46b-492">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3b46b-493">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3b46b-493">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3b46b-494">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3b46b-494">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3b46b-495">Uri</span><span class="sxs-lookup"><span data-stu-id="3b46b-495">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3b46b-496">Versão</span><span class="sxs-lookup"><span data-stu-id="3b46b-496">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3b46b-497">Tipos complexos</span><span class="sxs-lookup"><span data-stu-id="3b46b-497">Complex types</span></span>

<span data-ttu-id="3b46b-498">Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar.</span><span class="sxs-lookup"><span data-stu-id="3b46b-498">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3b46b-499">Quando ocorre model binding, a classe é instanciada usando o construtor padrão público.</span><span class="sxs-lookup"><span data-stu-id="3b46b-499">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3b46b-500">Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-500">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="3b46b-501">Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-501">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3b46b-502">Para associação a um parâmetro, o prefixo é o nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-502">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3b46b-503">Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="3b46b-503">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3b46b-504">Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3b46b-504">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3b46b-505">Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-505">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3b46b-506">Prefixo = nome do parâmetro</span><span class="sxs-lookup"><span data-stu-id="3b46b-506">Prefix = parameter name</span></span>

<span data-ttu-id="3b46b-507">Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-507">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3b46b-508">O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-508">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3b46b-509">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-509">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3b46b-510">Prefixo = nome da propriedade</span><span class="sxs-lookup"><span data-stu-id="3b46b-510">Prefix = property name</span></span>

<span data-ttu-id="3b46b-511">Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-511">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3b46b-512">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-512">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3b46b-513">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-513">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3b46b-514">Prefixo personalizado</span><span class="sxs-lookup"><span data-stu-id="3b46b-514">Custom prefix</span></span>

<span data-ttu-id="3b46b-515">Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-515">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3b46b-516">O model binding começa examinando as fontes para a chave `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-516">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3b46b-517">Se ela não for encontrada, procurará `ID` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-517">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3b46b-518">Atributos para destinos de tipo complexo</span><span class="sxs-lookup"><span data-stu-id="3b46b-518">Attributes for complex type targets</span></span>

<span data-ttu-id="3b46b-519">Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-519">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="3b46b-520">Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores.</span><span class="sxs-lookup"><span data-stu-id="3b46b-520">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3b46b-521">Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-521">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3b46b-522">O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="3b46b-522">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="3b46b-523">Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="3b46b-523">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="3b46b-524">Atributo [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="3b46b-524">[BindRequired] attribute</span></span>

<span data-ttu-id="3b46b-525">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3b46b-525">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3b46b-526">Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-526">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3b46b-527">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-527">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="3b46b-528">Atributo [BindNever]</span><span class="sxs-lookup"><span data-stu-id="3b46b-528">[BindNever] attribute</span></span>

<span data-ttu-id="3b46b-529">Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método.</span><span class="sxs-lookup"><span data-stu-id="3b46b-529">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3b46b-530">Impede que o model binding configure a propriedade de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-530">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3b46b-531">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-531">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="3b46b-532">Atributo [Bind]</span><span class="sxs-lookup"><span data-stu-id="3b46b-532">[Bind] attribute</span></span>

<span data-ttu-id="3b46b-533">Pode ser aplicado a uma classe ou a um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="3b46b-533">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3b46b-534">Especifica quais propriedades de um modelo devem ser incluídas no model binding.</span><span class="sxs-lookup"><span data-stu-id="3b46b-534">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="3b46b-535">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:</span><span class="sxs-lookup"><span data-stu-id="3b46b-535">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3b46b-536">No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:</span><span class="sxs-lookup"><span data-stu-id="3b46b-536">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3b46b-537">O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-537">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="3b46b-538">Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas.</span><span class="sxs-lookup"><span data-stu-id="3b46b-538">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3b46b-539">Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-539">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3b46b-540">Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3b46b-540">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="3b46b-541">Coleções</span><span class="sxs-lookup"><span data-stu-id="3b46b-541">Collections</span></span>

<span data-ttu-id="3b46b-542">Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-542">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3b46b-543">Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-543">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3b46b-544">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-544">For example:</span></span>

* <span data-ttu-id="3b46b-545">Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-545">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3b46b-546">Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-546">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="3b46b-547">O formato a seguir é compatível apenas com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="3b46b-547">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3b46b-548">Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-548">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3b46b-549">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="3b46b-549">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3b46b-550">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="3b46b-550">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3b46b-551">Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero.</span><span class="sxs-lookup"><span data-stu-id="3b46b-551">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3b46b-552">Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-552">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3b46b-553">Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-553">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3b46b-554">Dicionários</span><span class="sxs-lookup"><span data-stu-id="3b46b-554">Dictionaries</span></span>

<span data-ttu-id="3b46b-555">Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*.</span><span class="sxs-lookup"><span data-stu-id="3b46b-555">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="3b46b-556">Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-556">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3b46b-557">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-557">For example:</span></span>

* <span data-ttu-id="3b46b-558">Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-558">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3b46b-559">Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="3b46b-559">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="3b46b-560">Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-560">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3b46b-561">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3b46b-561">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3b46b-562">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="3b46b-562">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3b46b-563">Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta</span><span class="sxs-lookup"><span data-stu-id="3b46b-563">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3b46b-564">O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:</span><span class="sxs-lookup"><span data-stu-id="3b46b-564">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3b46b-565">Tratar valores como cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="3b46b-565">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3b46b-566">Espere que as URLs sejam invariáveis de cultura.</span><span class="sxs-lookup"><span data-stu-id="3b46b-566">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3b46b-567">Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura.</span><span class="sxs-lookup"><span data-stu-id="3b46b-567">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3b46b-568">Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.</span><span class="sxs-lookup"><span data-stu-id="3b46b-568">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3b46b-569">Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:</span><span class="sxs-lookup"><span data-stu-id="3b46b-569">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3b46b-570">Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3b46b-570">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3b46b-571">Copie o código de [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="3b46b-571">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3b46b-572">Substitua o [valor de cultura](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="3b46b-572">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3b46b-573">Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-573">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3b46b-574">Tipos de dados especiais</span><span class="sxs-lookup"><span data-stu-id="3b46b-574">Special data types</span></span>

<span data-ttu-id="3b46b-575">Há alguns tipos de dados especiais com o model binding pode lidar.</span><span class="sxs-lookup"><span data-stu-id="3b46b-575">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3b46b-576">IFormFile e IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="3b46b-576">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3b46b-577">Um arquivo carregado incluído na solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b46b-577">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3b46b-578">Também compatível com `IEnumerable<IFormFile>` para vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-578">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3b46b-579">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3b46b-579">CancellationToken</span></span>

<span data-ttu-id="3b46b-580">usado para cancelar a atividade em controladores assíncronos.</span><span class="sxs-lookup"><span data-stu-id="3b46b-580">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3b46b-581">FormCollection</span><span class="sxs-lookup"><span data-stu-id="3b46b-581">FormCollection</span></span>

<span data-ttu-id="3b46b-582">Usado para recuperar todos os valores dos dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-582">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3b46b-583">Formatadores de entrada</span><span class="sxs-lookup"><span data-stu-id="3b46b-583">Input formatters</span></span>

<span data-ttu-id="3b46b-584">Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato.</span><span class="sxs-lookup"><span data-stu-id="3b46b-584">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3b46b-585">Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-585">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3b46b-586">Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="3b46b-586">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3b46b-587">Você pode adicionar outros formatadores para outros tipos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3b46b-587">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3b46b-588">O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="3b46b-588">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3b46b-589">Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="3b46b-589">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3b46b-590">Para usar os formatadores de entrada XML internos:</span><span class="sxs-lookup"><span data-stu-id="3b46b-590">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3b46b-591">Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-591">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3b46b-592">Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="3b46b-592">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="3b46b-593">Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="3b46b-593">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3b46b-594">Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3b46b-594">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3b46b-595">Excluir tipos especificados do model binding</span><span class="sxs-lookup"><span data-stu-id="3b46b-595">Exclude specified types from model binding</span></span>

<span data-ttu-id="3b46b-596">O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="3b46b-596">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3b46b-597">Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="3b46b-597">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3b46b-598">Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-598">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3b46b-599">Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-599">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3b46b-600">Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-600">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="3b46b-601">Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-601">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3b46b-602">Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="3b46b-602">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="3b46b-603">Associadores de modelos personalizados</span><span class="sxs-lookup"><span data-stu-id="3b46b-603">Custom model binders</span></span>

<span data-ttu-id="3b46b-604">Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino.</span><span class="sxs-lookup"><span data-stu-id="3b46b-604">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3b46b-605">Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="3b46b-605">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3b46b-606">Model binding manual</span><span class="sxs-lookup"><span data-stu-id="3b46b-606">Manual model binding</span></span>

<span data-ttu-id="3b46b-607">O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="3b46b-607">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3b46b-608">O método é definido nas classes `ControllerBase` e `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="3b46b-608">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3b46b-609">Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-609">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3b46b-610">O método retornará `false` se o model binding falhar.</span><span class="sxs-lookup"><span data-stu-id="3b46b-610">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3b46b-611">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="3b46b-611">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="3b46b-612">Atributo [FromServices]</span><span class="sxs-lookup"><span data-stu-id="3b46b-612">[FromServices] attribute</span></span>

<span data-ttu-id="3b46b-613">O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados.</span><span class="sxs-lookup"><span data-stu-id="3b46b-613">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3b46b-614">Porém, não se trata de associar dados de um provedor de valor.</span><span class="sxs-lookup"><span data-stu-id="3b46b-614">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3b46b-615">Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3b46b-615">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3b46b-616">Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.</span><span class="sxs-lookup"><span data-stu-id="3b46b-616">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b46b-617">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3b46b-617">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
