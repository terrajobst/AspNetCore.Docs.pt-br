---
title: Criar APIs Web com o ASP.NET Core
author: scottaddie
description: Aprenda os fundamentos da criação de uma API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/11/2019
uid: web-api/index
ms.openlocfilehash: d804a7f1b4f0e89f433a3674116c97804705f7cc
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882951"
---
# <a name="create-web-apis-with-aspnet-core"></a>Criar APIs Web com o ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie) e [Tom Dykstra](https://github.com/tdykstra)

O ASP.NET Core permite a criação de serviços RESTful, também conhecidos como APIs Web, usando C#. Para lidar com solicitações, uma API Web usa controladores. Em uma API Web, os *controladores* são classes que derivam de `ControllerBase`. Este artigo mostra como usar controladores para lidar com solicitações da API.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([Como baixar](xref:index#how-to-download-a-sample).)

## <a name="controllerbase-class"></a>Classe ControllerBase

Uma API Web tem uma ou mais classes de controlador derivadas de <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Por exemplo, o modelo de projeto de API Web cria um controlador de Valores:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

Não crie um controlador de API Web derivando da classe base <xref:Microsoft.AspNetCore.Mvc.Controller>. `Controller` é derivado de `ControllerBase` e agrega suporte para exibições; portanto, serve para manipulação de páginas da Web, não para solicitações de API Web.  Há uma exceção a essa regra: se você pretende usar o mesmo controlador para exibições e APIs, derive-o de `Controller`.

A classe `ControllerBase` fornece muitas propriedades e métodos úteis para lidar com solicitações HTTP. Por exemplo, `ControllerBase.CreatedAtAction` retorna um código de status 201:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 Veja mais alguns exemplos de métodos fornecidos por `ControllerBase`.

|Método  |Observações  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| Retorna o código de status 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |Retorna o código de status 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|Retorna um arquivo.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|Invoca [model binding](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|Invoca [validação de modelo](xref:mvc/models/validation).|

Confira uma lista com todos os métodos e propriedades disponíveis em <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Atributos

O namespace <xref:Microsoft.AspNetCore.Mvc> fornece atributos que podem ser usados para configurar o comportamento de controladores de API Web e dos métodos de ação. O exemplo a seguir usa atributos para especificar o método HTTP aceito e os códigos de status retornado:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Confira mais alguns exemplos de atributos disponíveis.

|Atributo|Observações|
|---------|-----|
|[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Especifica o padrão de URL para um controlador ou ação.|
|[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Especifica o prefixo e as propriedades que serão incluídos no model binding.|
|[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifica uma ação que dá suporte ao método HTTP GET.|
|[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Especifica os tipos de dados aceitos por uma ação.|
|[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Especifica os tipos de dados retornados por uma ação.|

Veja uma lista que inclui os atributos disponíveis no namespace <xref:Microsoft.AspNetCore.Mvc>.

## <a name="apicontroller-attribute"></a>Atributo ApiController

O atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) pode ser aplicado a uma classe de controlador para habilitar comportamentos específicos à API:

* [Requisito de roteamento de atributo](#attribute-routing-requirement)
* [Respostas HTTP 400 automáticas](#automatic-http-400-responses)
* [Inferência de parâmetro de origem da associação](#binding-source-parameter-inference)
* [Inferência de solicitação de várias partes/dados de formulário](#multipartform-data-request-inference)
* [Detalhes do problema dos códigos de status de erro](#problem-details-for-error-status-codes)

Esses recursos exigem [compatibilidade com a versão](<xref:mvc/compatibility-version>) 2.1 ou posterior.

### <a name="apicontroller-on-specific-controllers"></a>ApiController em controladores específicos

O atributo `[ApiController]` pode ser aplicado a controladores específicos, como no exemplo a seguir do modelo de projeto:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a>ApiController em vários controladores

Uma abordagem ao uso do atributo em mais de um controlador é a criação de uma classe de controlador base personalizada anotada com o atributo `[ApiController]`. Veja um exemplo que mostra uma classe base personalizada e um controlador derivado dela:

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a>ApiController em um assembly

Se [versão de compatibilidade](<xref:mvc/compatibility-version>) estiver definida como 2.2 ou posterior, o atributo `[ApiController]` poderá ser aplicado a um assembly. A anotação dessa maneira aplica o comportamento da API Web para todos os controladores no assembly. Não é possível recusar controladores individuais. Aplique o atributo no nível do assembly à classe `Startup` conforme mostra o exemplo a seguir:

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

## <a name="attribute-routing-requirement"></a>Requisito de roteamento de atributo

O atributo `ApiController` transforma em requisito o roteamento de atributo. Por exemplo:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

As ações são inacessíveis por meio de [rotas convencionais](xref:mvc/controllers/routing#conventional-routing) definidas por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> em `Startup.Configure`.

## <a name="automatic-http-400-responses"></a>Respostas automáticas do HTTP 400

O atributo `ApiController` faz com que os erros de validação do modelo disparem automaticamente uma resposta HTTP 400. Consequentemente, o código a seguir se torna desnecessário em um método de ação:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a>Resposta BadRequest padrão 

Com uma versão de compatibilidade de 2.2 ou posterior, o tipo de resposta padrão para respostas HTTP 400 é <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. O tipo `ValidationProblemDetails` está em conformidade com a [especificação RFC 7807](https://tools.ietf.org/html/rfc7807).

Para alterar a resposta padrão para <xref:Microsoft.AspNetCore.Mvc.SerializableError>, defina a propriedade `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` como `true` em `Startup.ConfigureServices`, conforme mostra o exemplo a seguir:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a>Personalizar a resposta de BadRequest

Para personalizar a resposta resultante de um erro de validação, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>. Adicione o código realçado a seguir após `services.AddMvc().SetCompatibilityVersion`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="disable-automatic-400"></a>Desabilitar 400 automática

Para desabilitar o comportamento 400 automático, defina a propriedade <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> como `true`. Adicione o código realçado a seguir em `Startup.ConfigureServices` após `services.AddMvc().SetCompatibilityVersion`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a>Inferência de parâmetro de origem de associação

Um atributo de origem de associação define o local no qual o valor do parâmetro de uma ação é encontrado. Os seguintes atributos da origem da associação existem:

|Atributo|Fonte de associação |
|---------|---------|
|[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Corpo da solicitação |
|[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Dados do formulário no corpo da solicitação |
|[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Cabeçalho da solicitação |
|[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Parâmetro de cadeia de caracteres de consulta de solicitação |
|[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Dados de rota da solicitação atual |
|[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | O serviço de solicitação inserido como um parâmetro de ação |

> [!WARNING]
> Não use `[FromRoute]` quando os valores puderem conter `%2f` (ou seja, `/`). `%2f` não ficará sem escape para `/`. Use `[FromQuery]`, se o valor puder conter `%2f`.

Sem o atributo `[ApiController]` ou outros atributos de origem da associação, como `[FromQuery]`, o tempo de execução do ASP.NET Core tenta usar o associador de modelos de objeto complexo. O associador de modelos de objeto complexo extrai os dados dos provedores de valor em uma ordem definida.

No exemplo a seguir, o atributo `[FromQuery]` indica que o valor do parâmetro `discontinuedOnly` é fornecido na cadeia de caracteres de consulta da URL de solicitação:

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

O atributo `[ApiController]` aplica regras de inferência para as fontes de dados padrão dos parâmetros de ação. Essas regras poupam você da necessidade de identificar as origens de associação manualmente aplicando atributos aos parâmetros de ação. As regras de inferência da origem de associação se comportam da seguinte maneira:

* `[FromBody]` é inferido para parâmetros de tipo complexo. Uma exceção à regra de inferência `[FromBody]` é qualquer tipo interno complexo com um significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> e <xref:System.Threading.CancellationToken>. O código de inferência da origem da associação ignora esses tipos especiais. 
* `[FromForm]` é inferido para parâmetros de ação do tipo <xref:Microsoft.AspNetCore.Http.IFormFile> e <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. Ele não é inferido para qualquer tipo simples ou definido pelo usuário.
* `[FromRoute]` é inferido para qualquer nome de parâmetro de ação correspondente a um parâmetro no modelo de rota. Quando mais de uma rota correspondem a um parâmetro de ação, qualquer valor de rota é considerado `[FromRoute]`.
* `[FromQuery]` é inferido para todos os outros parâmetros de ação.

### <a name="frombody-inference-notes"></a>Notas de inferência FromBody

`[FromBody]` não é inferido para tipos simples, como `string` ou `int`. Portanto, o atributo `[FromBody]` deve ser usado para tipos simples quando essa funcionalidade for necessária.

Quando uma ação tiver mais de um parâmetro associado ao corpo da solicitação, uma exceção será lançada. Por exemplo, todas as assinaturas de método de ação a seguir causam uma exceção:

* `[FromBody]` inferido em ambos, pois são tipos complexos.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* O atributo `[FromBody]` em um, inferido no outro, porque é um tipo complexo.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* Atributo `[FromBody]` em ambos.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> No ASP.NET Core 2.1, os parâmetros de tipo de coleção, como listas e matrizes, são inferidos incorretamente como `[FromQuery]`. O atributo `[FromBody]` deve ser usado para esses parâmetros se eles forem vinculados ao corpo da solicitação. Esse comportamento é corrigido no ASP.NET Core 2.2 ou posterior, onde os parâmetros do tipo de coleção são inferidos para serem associados ao corpo por padrão.

### <a name="disable-inference-rules"></a>Desabilitar regras de inferência

Para desabilitar a inferência da origem da associação, defina <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> como `true`. Adicione o seguinte código em `Startup.ConfigureServices` após `services.AddMvc().SetCompatibilityVersion`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a>Inferência de solicitação de várias partes/dados de formulário

O atributo `[ApiController]` aplicar uma regra de inferência quando um parâmetro de ação é anotado com o atributo [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute): o tipo de conteúdo de solicitação `multipart/form-data` é inferido.

Para desabilitar o comportamento padrão, defina <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> como `true` em `Startup.ConfigureServices`, conforme mostra o exemplo a seguir:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a>Detalhes do problema dos códigos de status de erro

Quando a versão de compatibilidade for 2.2 ou posterior, o MVC transformará um resultado de erro (um resultado com o código de status 400 ou superior) em um resultado com <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. O tipo `ProblemDetails` tem base na [especificação RFC 7807](https://tools.ietf.org/html/rfc7807) para fornecer detalhes de erro legíveis por computador em uma resposta HTTP.

Considere o seguinte código em uma ação do controlador:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

A resposta HTTP para `NotFound` tem um código de status 404 com um corpo `ProblemDetails`. Por exemplo:

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a>Personalizar a resposta de ProblemDetails

Use a propriedade `ClientErrorMapping` para configurar o conteúdo da resposta `ProblemDetails`. Por exemplo, o código a seguir atualiza a propriedade `type` para respostas 404:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a>Desabilitar a resposta de ProblemDetails

A criação automática de `ProblemDetails` fica desabilitada quando a propriedade `SuppressMapClientErrors` é definida como `true`. Adicione o seguinte código em `Startup.ConfigureServices`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a>Recursos adicionais 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
