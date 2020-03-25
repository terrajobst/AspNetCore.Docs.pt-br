---
title: Roteamento para ações do controlador no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core MVC usa o middleware de roteamento para corresponder a URLs das solicitações de entrada e mapeá-las para ações.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: be7da9eeaf64c2f52c095b5179ccc22db43d57c3
ms.sourcegitcommit: 99e71ae03319ab386baf2ebde956fc2d511df8b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80242563"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Roteamento para ações do controlador no ASP.NET Core

Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Os controladores de ASP.NET Core usam o [middleware](xref:fundamentals/middleware/index) de roteamento para corresponder as URLs de solicitações de entrada e mapeá-las para [ações](#action).  Modelos de rotas:

* São definidos em código ou atributos de inicialização.
* Descreva como os caminhos de URL são correspondidos às [ações](#action).
* São usados para gerar URLs para links. Os links gerados são normalmente retornados em respostas.

As ações são [roteadas por convenção](#cr) ou [roteadas por atributo](#ar). Colocar uma rota no controlador ou na [ação](#action) a torna roteada por atributo. Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).

Este documento:

* Explica as interações entre o MVC e o roteamento:
  * Como os aplicativos MVC típicos fazem uso dos recursos de roteamento.
  * Aborda ambos:
    * [Roteamento convencional](#cr) normalmente usado com controladores e exibições.
    * *Roteamento de atributos* usado com APIs REST. Se você estiver interessado principalmente no roteamento de APIs REST, vá para a seção [Roteamento de atributo para APIs REST](#ar) .
  * Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes avançados de roteamento.
* Refere-se ao sistema de roteamento padrão adicionado no ASP.NET Core 3,0, chamado roteamento de ponto de extremidade. É possível usar controladores com a versão anterior do roteamento para fins de compatibilidade. Consulte o [Guia de migração 2.2-3.0](xref:migration/22-to-30) para obter instruções. Consulte a [versão 2,2 deste documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para obter material de referência sobre o sistema de roteamento herdado.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Configurar a rota convencional

o `Startup.Configure` normalmente tem um código semelhante ao seguinte ao usar o [Roteamento convencional](#crd):

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Dentro da chamada para <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> é usado para criar uma única rota. A rota única é denominada `default` rota. A maioria dos aplicativos com controladores e exibições usa um modelo de rota semelhante ao `default` rota. As APIs REST devem usar o [Roteamento de atributos](#ar).

O modelo de rota `"{controller=Home}/{action=Index}/{id?}"`:

* Corresponde a um caminho de URL como `/Products/Details/5`
* Extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` pela geração de tokens do caminho. A extração dos valores de rota resultará em uma correspondência se o aplicativo tiver um controlador chamado `ProductsController` e uma ação `Details`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

 O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.

  * `/Products/Details/5` modelo associa o valor de `id = 5` para definir o parâmetro `id` como `5`. Consulte [Associação de modelo](xref:mvc/models/model-binding) para obter mais detalhes.
* `{controller=Home}` define `Home` como o `controller`padrão.
* `{action=Index}` define `Index` como o `action`padrão.
*  O caractere `?` no `{id?}` define `id` como opcional.
  * Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência. Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.
* Corresponde ao caminho da URL `/`.
* Produz os valores de rota `{ controller = Home, action = Index }`.
* O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.

Os valores para `controller` e `action` fazem uso dos valores padrão. `id` não produz um valor, pois não há nenhum segmento correspondente no caminho da URL. `/` só corresponde se existir uma `HomeController` e `Index` ação:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Usando a definição de controlador e o modelo de rota anteriores, a ação de `HomeController.Index` é executada para os seguintes caminhos de URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

O caminho da URL `/` usa os controladores de `Home` padrão do modelo de rota e a ação de `Index`. O caminho da URL `/Home` usa a ação de `Index` padrão do modelo de rota.

O método de conveniência <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Substituto

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

O roteamento é configurado usando o <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> e o middleware de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>. Para usar controladores:

* Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> dentro de `UseEndpoints` para mapear controladores [roteados de atributo](#ar) .
* Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ou <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>para mapear controladores [roteados de Convenção](#cr) .

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Roteamento convencional

O roteamento convencional é usado com controladores e exibições. A rota `default`:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

é um exemplo de *roteamento convencional*. Ele é chamado de *Roteamento convencional* porque estabelece uma *Convenção* para caminhos de URL:

* O primeiro segmento de caminho, `{controller=Home}`, é mapeado para o nome do controlador.
* O segundo segmento, `{action=Index}`, é mapeado para o nome da [ação](#action) .
* O terceiro segmento, `{id?}` é usado para um `id`opcional. O `?` no `{id?}` o torna opcional. `id` é usado para mapear para uma entidade de modelo.

Usando essa `default` rota, o caminho da URL:

* `/Products/List` mapeia para a ação de `ProductsController.List`.
* `/Blog/Article/17` mapeia para `BlogController.Article` e, normalmente, o modelo associa o parâmetro `id` a 17.

Este mapeamento:

* É baseado **somente**nos nomes de controlador e de [ação](#action) .
* Não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.

O uso do roteamento convencional com a rota padrão permite a criação do aplicativo sem a necessidade de surgir um novo padrão de URL para cada ação. Para um aplicativo com ações de estilo [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , tendo consistência para as URLs entre controladores:

* Ajuda a simplificar o código.
* Torna a interface do usuário mais previsível.

> [!WARNING]
> O `id` no código anterior é definido como opcional pelo modelo de rota. As ações podem ser executadas sem a ID opcional fornecida como parte da URL. Em geral, quando`id` é omitido da URL:
>
> * `id` é definido como `0` por associação de modelo.
> * Nenhuma entidade encontrada no `id == 0`correspondente ao banco de dados.
>
> O [Roteamento de atributos](#ar) fornece controle refinado para tornar a ID necessária para algumas ações e não para outras. Por convenção, a documentação inclui parâmetros opcionais como `id` quando é provável que eles apareçam no uso correto.

A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas. A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:

* Dá suporte a um esquema de roteamento básico e descritivo.
* É um ponto de partida útil para aplicativos baseados em interface do usuário.
* É o único modelo de rota necessário para muitos aplicativos da interface do usuário da Web. Para aplicativos de interface do usuário da Web maiores, outra rota usando [áreas](#areas) , se for sempre tudo o que for necessário.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:

* Atribua automaticamente um valor de **pedido** aos seus pontos de extremidade com base na ordem em que são invocados.

Roteamento de ponto de extremidade no ASP.NET Core 3,0 e posterior:

* Não tem um conceito de rotas.
* Não fornece garantias de ordenação para a execução de extensibilidade, todos os pontos de extremidade são processados de uma só vez.

Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.

O [Roteamento de atributos](#ar) é explicado mais adiante neste documento.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Várias rotas convencionais

Várias [rotas convencionais](#cr) podem ser adicionadas dentro de `UseEndpoints` adicionando mais chamadas para <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>. Isso permite a definição de várias convenções ou a adição de rotas convencionais que são dedicadas a uma [ação](#action)específica, como:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

A rota de `blog` no código anterior é uma **rota convencional dedicada**. Ele é chamado de rota convencional dedicada porque:

* Ele usa o [Roteamento convencional](#cr).
* Ele é dedicado a uma [ação](#action)específica.

Como `controller` e `action` não aparecem no modelo de rota `"blog/{*article}"` como parâmetros:

* Eles só podem ter os valores padrão `{ controller = "Blog", action = "Article" }`.
* Essa rota sempre é mapeada para a ação `BlogController.Article`.

`/Blog`, `/Blog/Article`e `/Blog/{any-string}` são os únicos caminhos de URL que correspondem à rota do blog.

O exemplo anterior:

* `blog` rota tem uma prioridade mais alta para correspondências do que a rota de `default`, pois ela é adicionada primeiro.
* É um exemplo de roteamento de estilo de separador, onde é comum ter um [nome de artigo](https://developer.mozilla.org/docs/Glossary/Slug) como parte da URL.

> [!WARNING]
> No ASP.NET Core 3,0 e posterior, o roteamento não:
> * Defina um conceito chamado *rota*. `UseRouting` adiciona a correspondência de rota ao pipeline de middleware. O middleware de `UseRouting` examina o conjunto de pontos de extremidade definidos no aplicativo e seleciona a melhor correspondência de ponto final com base na solicitação.
> * Forneça garantias sobre a ordem de execução de extensibilidade, como <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> ou <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.
>
>Consulte [Roteamento](xref:fundamentals/routing) para obter material de referência sobre roteamento.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Ordem de roteamento convencional

O roteamento convencional só corresponde a uma combinação de ação e controlador que são definidos pelo aplicativo. Isso se destina a simplificar os casos em que as rotas convencionais se sobrepõem.
Adicionar rotas usando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> atribuir automaticamente um valor de pedido a seus pontos de extremidade com base na ordem em que são invocados. As correspondências de uma rota que aparece anteriormente têm uma prioridade mais alta. O roteamento convencional é dependente da ordem. Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área. [Rotas convencionais dedicadas](#dcr) com [parâmetros de rota](xref:fundamentals/routing#greedy)catch all como `{*article}` podem tornar uma rota muito grande, o que significa que ela corresponde às URLs que você pretende corresponder a outras rotas. Coloque as rotas de ávido mais tarde na tabela de rotas para evitar correspondências de caminhada.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Resolvendo ações ambíguas

Quando dois pontos de extremidade correspondem ao roteamento, o roteamento deve seguir um destes procedimentos:

* Escolha o melhor candidato.
* Gera uma exceção.

Por exemplo:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

O controlador anterior define duas ações que correspondem a:

* O caminho da URL `/Products33/Edit/17`
* `{ controller = Products33, action = Edit, id = 17 }`de dados de rota.

Esse é um padrão típico para controladores MVC:

* `Edit(int)` exibe um formulário para editar um produto.
* `Edit(int, Product)` processa o formulário Postado.

Para resolver a rota correta:

* `Edit(int, Product)` é selecionado quando a solicitação é um `POST`HTTP.
* `Edit(int)` é selecionado quando o [verbo http](#verb) é qualquer outra coisa. `Edit(int)` geralmente é chamado via `GET`.

O <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, é fornecido ao roteamento para que ele possa escolher com base no método HTTP da solicitação. O `HttpPostAttribute` faz `Edit(int, Product)` uma correspondência melhor do que `Edit(int)`.

É importante entender a função de atributos, como `HttpPostAttribute`. Atributos semelhantes são definidos para outros [verbos HTTP](#verb). No [Roteamento convencional](#cr), é comum que as ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de Mostrar formulário, enviar formulário. Por exemplo, consulte [examinar os dois métodos de ação de edição](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Se o roteamento não puder escolher um melhor candidato, um <xref:System.Reflection.AmbiguousMatchException> será lançado, listando os vários pontos de extremidade correspondentes.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Nomes de rota convencionais

As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas convencionais:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Os nomes de rota fornecem um nome lógico à rota. A rota nomeada pode ser usada para a geração de URL. O uso de uma rota nomeada simplifica a criação de URL quando a ordenação de rotas pode tornar a geração de URL complicada. Os nomes de rota devem ser de todo o aplicativo.

Nomes de rota:

* Não têm impacto sobre a correspondência de URL ou o tratamento de solicitações.
* São usados somente para a geração de URL.

O conceito de nome de rota é representado no roteamento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Os termos **nome da rota** e **nome do ponto de extremidade**:

* São intercambiáveis.
* Qual delas é usada na documentação e no código depende da API que está sendo descrita.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Roteamento de atributos para APIs REST

As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por [verbos HTTP](#verb).

O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota. O código de `StartUp.Configure` a seguir é típico para uma API REST e é usado no próximo exemplo:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> é chamado dentro de `UseEndpoints` para mapear controladores roteados de atributo.

No exemplo a seguir:

* O método de `Configure` anterior é usado.
* `HomeController` corresponde a um conjunto de URLs semelhantes ao que a rota convencional padrão `{controller=Home}/{action=Index}/{id?}` corresponde.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

A ação `HomeController.Index` é executada para qualquer um dos caminhos de URL `/`, `/Home`, `/Home/Index`ou `/Home/Index/3`.

Este exemplo realça uma importante diferença de programação entre roteamento de atributo e [Roteamento convencional](#cr). O roteamento de atributos requer mais entrada para especificar uma rota. A rota padrão convencional lida com as rotas mais sucintamente. No entanto, o roteamento de atributos permite e exige um controle preciso de quais modelos de rota se aplicam a cada [ação](#action).

Com o roteamento de atributos, o nome do controlador e os nomes de ação não desempenham **nenhuma** função na qual a ação é correspondida. O exemplo a seguir corresponde às mesmas URLs do exemplo anterior:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

O código a seguir usa a substituição de token para `action` e `controller`:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

O código a seguir aplica-se `[Route("[controller]/[action]")]` ao controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

No código anterior, os modelos de método de `Index` devem preceder `/` ou `~/` aos modelos de rota. Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.

Consulte [precedência do modelo de rota](xref:fundamentals/routing#rtp) para obter informações sobre a seleção do modelo de rota.

## <a name="reserved-routing-names"></a>Nomes reservados de roteamento

As seguintes palavras-chave são nomes de parâmetro de rota reservados ao usar controladores ou Razor Pages:

* `action`
* `area`
* `controller`
* `handler`
* `page`

O uso de `page` como um parâmetro de rota com roteamento de atributo é um erro comum. Fazer isso resulta em comportamento inconsistente e confuso com a geração de URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Os nomes de parâmetro especiais são usados pela geração de URL para determinar se uma operação de geração de URL refere-se a uma página Razor ou a um controlador.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Modelos de verbo HTTP

ASP.NET Core tem os seguintes modelos de verbo HTTP:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Modelos de rota

ASP.NET Core tem os seguintes modelos de rota:

* Todos os [modelos de verbo http](#verb) são modelos de rota.
* [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Roteamento de atributos com atributos de verbo http

Considere o seguinte controlador:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

No código anterior:

* Cada ação contém o atributo `[HttpGet]`, que restringe a correspondência somente a solicitações HTTP GET.
* A ação de `GetProduct` inclui o modelo de `"{id}"`, portanto `id` é anexado ao modelo `"api/[controller]"` no controlador. O modelo de métodos é `"api/[controller]/"{id}""`. Portanto, essa ação só corresponde às solicitações GET do formulário `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* A ação `GetIntProduct` contém o modelo de `"int/{id:int}")`. A parte `:int` do modelo restringe os valores de rota `id` para cadeias de caracteres que podem ser convertidas em um número inteiro. Uma solicitação GET para `/api/test2/int/abc`:
  * Não corresponde a esta ação.
  * Retorna um erro [404 não encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* A ação de `GetInt2Product` contém `{id}` no modelo, mas não restringe `id` a valores que podem ser convertidos em um número inteiro. Uma solicitação GET para `/api/test2/int2/abc`:
  * Corresponde a essa rota.
  * A associação de modelo falha ao converter `abc` em um inteiro. O parâmetro `id` do método é inteiro.
  * Retorna uma [solicitação inadequada 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque a associação de modelo falhou ao converter`abc` em um inteiro.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

O roteamento de atributos pode usar atributos de <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>e <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>. Todos os atributos de [verbo http](#verb) aceitam um modelo de rota. O exemplo a seguir mostra duas ações que correspondem ao mesmo modelo de rota:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Usando o caminho da URL `/products3`:

* A ação `MyProductsController.ListProducts` é executada quando o [verbo http](#verb) é `GET`.
* A ação `MyProductsController.CreateProduct` é executada quando o [verbo http](#verb) é `POST`.

Ao criar uma API REST, é raro que você precise usar `[Route(...)]` em um método de ação, pois a ação aceita todos os métodos HTTP. É melhor usar o [atributo de verbo http](#verb) mais específico para ser preciso em relação ao que sua API dá suporte. Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.

As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por verbos HTTP. Isso significa que muitas operações, por exemplo, GET e POST no mesmo recurso lógico usam a mesma URL. O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.

Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota. No exemplo a seguir, `id` é necessário como parte do caminho da URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

A ação `Products2ApiController.GetProduct(int)`:

* É executado com caminho de URL como `/products2/3`
* Não é executado com o caminho da URL `/products2`.

O atributo [[consume]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que uma ação limite os tipos de conteúdo de solicitação com suporte. Para obter mais informações, consulte [definir tipos de conteúdo de solicitação com suporte com o atributo consumes](xref:web-api/index#consumes).

 Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.

Para obter mais informações sobre `[ApiController]`, consulte [atributo ApiController](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nome da rota

O código a seguir define um nome de rota de `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica. Nomes de rota:

* Não têm impacto sobre o comportamento de correspondência de URL do roteamento.
* São usados somente para a geração de URL.

Nomes de rotas devem ser exclusivos no nível do aplicativo.

Compare o código anterior com a rota padrão convencional, que define o parâmetro `id` como opcional (`{id?}`). A capacidade de especificar com precisão as APIs tem vantagens, como permitir que `/products` e `/products/5` sejam expedidas para ações diferentes.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Combinando rotas de atributo

Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais. Modelos de rota definidos no controlador precedem modelos de rota nas ações. Colocar um atributo de rota no controlador faz com que  **todas** as ações no controlador usem o roteamento de atributo.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

No exemplo anterior:

* O caminho da URL `/products` pode corresponder `ProductsApi.ListProducts`
* O caminho da URL `/products/5` pode corresponder `ProductsApi.GetProduct(int)`.

Ambas as ações correspondem somente a `GET` HTTP porque elas são marcadas com o atributo `[HttpGet]`.

Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador. O exemplo a seguir corresponde a um conjunto de caminhos de URL semelhante à rota padrão.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

A tabela a seguir explica os atributos de `[Route]` no código anterior:

| Atributo               | Combina com `[Route("Home")]` | Define o modelo de rota |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Sim | `"Home"` |
| `[Route("Index")]` | Sim | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Sim | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Ordem de rota de atributo

O roteamento cria uma árvore e corresponde a todos os pontos de extremidade simultaneamente:

* As entradas de rota se comportam como se colocadas em uma ordenação ideal.
* As rotas mais específicas têm a chance de serem executadas antes das rotas mais gerais.

Por exemplo, uma rota de atributo como `blog/search/{topic}` é mais específica do que uma rota de atributo como `blog/{*article}`. A rota de `blog/search/{topic}` tem prioridade mais alta, por padrão, porque é mais específica. Usando o [Roteamento convencional](#cr), o desenvolvedor é responsável por colocar rotas na ordem desejada.

As rotas de atributo podem configurar um pedido usando a propriedade <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>. Todos os [atributos de rota](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) fornecidos pelo framework incluem `Order`. As rotas são processadas segundo uma classificação crescente da propriedade `Order`. A ordem padrão é `0`. Definir uma rota usando `Order = -1` é executado antes das rotas que não definem um pedido. Definir uma rota usando `Order = 1` é executado após a ordenação de rota padrão.

**Evite** , dependendo do `Order`. Se o espaço de URL de um aplicativo exigir valores de ordem explícitos para rotear corretamente, isso provavelmente será confuso para os clientes também. Em geral, o roteamento de atributos seleciona a rota correta com correspondência de URL. Se a ordem padrão usada para a geração de URL não estiver funcionando, usar um nome de rota como uma substituição é geralmente mais simples do que aplicar a propriedade `Order`.

Considere os dois controladores a seguir que definem a rota correspondente `/home`:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Solicitar `/home` com o código anterior gera uma exceção semelhante à seguinte:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Adicionar `Order` a um dos atributos de rota resolve a ambiguidade:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Com o código anterior, `/home` executa o ponto de extremidade `HomeController.Index`. Para obter o `MyDemoController.MyIndex`, solicite `/home/MyIndex`. **Observação**:

* O código anterior é um exemplo de design de roteamento insatisfatório. Ele foi usado para ilustrar a propriedade `Order`.
* A propriedade `Order` resolve apenas a ambiguidade, o modelo não pode ser correspondido. Seria melhor remover o modelo de `[Route("Home")]`.

Consulte [Razor Pages rota e convenções de aplicativo: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order) para obter informações sobre a ordem de rota com Razor Pages.

Em alguns casos, um erro HTTP 500 é retornado com rotas ambíguas. Use o [registro em log](xref:fundamentals/logging/index) para ver quais pontos de extremidade causaram a `AmbiguousMatchException`.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Substituição de token em modelos de rota [controlador], [ação], [área]

Para sua conveniência, as rotas de atributo dão suporte à substituição de token para parâmetros de rota reservados ao colocar um token em um dos seguintes:

* Chaves: `[]`
* Chaves: `{}`

Os tokens `[action]`, `[area]`e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

No código anterior:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Corresponde a `/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Corresponde a `/Products0/Edit/{id}`

A substituição de token ocorre como a última etapa da criação das rotas de atributo. O exemplo anterior se comporta da mesma forma que o código a seguir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Rotas de atributo também podem ser combinadas com herança. Isso é poderoso combinado com a substituição de token. A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`gera um nome de rota exclusivo para cada ação:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
gera um nome de rota exclusivo para cada ação.

Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar um transformador de parâmetro para personalizar a substituição de token

A substituição do token pode ser personalizada usando um transformador de parâmetro. Um transformador de parâmetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e transforma o valor dos parâmetros. Por exemplo, um transformador de parâmetro de `SlugifyParameterTransformer` personalizado altera o `SubscriptionManagement` valor de rota para `subscription-management`:

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

O <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> é uma convenção de modelo de aplicativo que:

* Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.
* Personaliza os valores de token de rota de atributo conforme eles são substituídos.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

O método `ListAll` anterior corresponde a `/subscription-management/list-all`.

O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Consulte [MDN Web docs no](https://developer.mozilla.org/docs/Glossary/Slug) separador para obter a definição do separador.

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Várias rotas de atributos

O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação. O uso mais comum disso é imitar o comportamento da rota convencional padrão, conforme mostrado no exemplo a seguir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Colocar vários atributos de rota no controlador significa que cada um combina com cada um dos atributos de rota nos métodos de ação:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Todas as restrições de rota de [verbo http](#verb) implementam `IActionConstraint`.

Quando vários atributos de rota que implementam <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> são colocados em uma ação:

* Cada restrição de ação combina com o modelo de rota aplicado ao controlador.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

O uso de várias rotas em ações pode parecer útil e eficiente, é melhor manter o espaço de URL básico e bem definido do aplicativo. Use várias rotas em ações **somente** quando necessário, por exemplo, para dar suporte a clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo

Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

No código anterior, `[HttpPost("product/{id:int}")]` aplica uma restrição de rota. A ação `ProductsController.ShowProduct` é correspondida somente por caminhos de URL como `/product/3`. A parte do modelo de rota `{id:int}` restringe esse segmento a apenas inteiros.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de rota personalizados usando IRouteTemplateProvider

Todos os [atributos de rota](#rt) implementam <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. O tempo de execução de ASP.NET Core:

* Procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado.
* Usa os atributos que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.

Implemente `IRouteTemplateProvider` para definir atributos de rota personalizados. Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

O método `Get` anterior retorna `Order = 2, Template = api/MyTestApi`.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Usar o modelo de aplicativo para personalizar as rotas de atributo

O modelo de aplicativo:

* É um modelo de objeto criado na inicialização.
* Contém todos os metadados usados pelo ASP.NET Core para rotear e executar as ações em um aplicativo.

O modelo de aplicativo inclui todos os dados coletados dos atributos de rota. Os dados dos atributos de rota são fornecidos pela implementação de `IRouteTemplateProvider`. Regras

* Pode ser escrito para modificar o modelo de aplicativo para personalizar a forma como o roteamento se comporta.
* São lidos na inicialização do aplicativo.

Esta seção mostra um exemplo básico de personalização de roteamento usando o modelo de aplicativo. O código a seguir torna as rotas aproximadamente alinhadas com a estrutura de pastas do projeto.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

O código a seguir impede que a Convenção de `namespace` seja aplicada a controladores que são roteados por atributo:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Por exemplo, o controlador a seguir não usa `NamespaceRoutingConvention`:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

O método `NamespaceRoutingConvention.Apply`:

* Não fará nada se o controlador for um atributo roteado.
* Define o modelo de controladores com base na `namespace`, com a `namespace` de base removida.

O `NamespaceRoutingConvention` pode ser aplicado no `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Por exemplo, considere o seguinte controlador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

No código anterior:

* O `namespace` base é `My.Application`.
* O nome completo do controlador anterior é `My.Application.Admin.Controllers.UsersController`.
* O `NamespaceRoutingConvention` define o modelo de controladores como `Admin/Controllers/Users/[action]/{id?`.

O `NamespaceRoutingConvention` também pode ser aplicado como um atributo em um controlador:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Roteamento misto: roteamento de atributo versus roteamento convencional

ASP.NET Core aplicativos podem misturar o uso de roteamento convencional e roteamento de atributos. É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.

As ações são roteadas convencionalmente ou segundo os atributos. Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo. Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa. **Qualquer** atributo de rota no controlador faz com que **todas as** ações no atributo do controlador sejam roteadas.

Roteamento de atributos e roteamento convencional usam o mesmo mecanismo de roteamento.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Geração de URL e valores de ambiente

Os aplicativos podem usar os recursos de geração de URL de roteamento para gerar links de URL para ações. A geração de URLs elimina URLs de codificação, tornando o código mais robusto e passível de manutenção. Esta seção se concentra nos recursos de geração de URL fornecidos pelo MVC e aborda apenas noções básicas de como funciona a geração de URL. Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.

A interface <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> é o elemento subjacente da infraestrutura entre o MVC e o roteamento para a geração de URL. Uma instância de `IUrlHelper` está disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.

No exemplo a seguir, a interface `IUrlHelper` é usada por meio da propriedade `Controller.Url` para gerar uma URL para outra ação.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho da URL `/UrlGeneration/Destination`. Esse caminho de URL é criado pelo roteamento combinando:

* Os valores de rota da solicitação atual, que são chamados de **valores de ambiente**.
* Os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente. Um parâmetro de rota que não tem um valor pode:

* Use um valor padrão se ele tiver um.
* Será ignorado se for opcional. Por exemplo, o `id` do modelo de rota `{controller}/{action}/{id?}`.

A geração de URL falhará se qualquer parâmetro de rota necessário não tiver um valor correspondente. Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.

O exemplo anterior de `Url.Action` pressupõe [Roteamento convencional](#cr). A geração de URL funciona de forma semelhante ao [Roteamento de atributos](#ar), embora os conceitos sejam diferentes. Com roteamento convencional:

* Os valores de rota são usados para expandir um modelo.
* Os valores de rota para `controller` e `action` geralmente aparecem nesse modelo. Isso funciona porque as URLs correspondidas pelo roteamento aderem a uma convenção.

O exemplo a seguir usa roteamento de atributo:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

A ação de `Source` no código anterior gera `custom/url/to/destination`.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> foi adicionado no ASP.NET Core 3,0 como uma alternativa ao `IUrlHelper`. o `LinkGenerator` oferece uma funcionalidade semelhante, mas mais flexível. Cada um dos outros métodos em `IUrlHelper` também tem uma família correspondente de métodos em `LinkGenerator`.

### <a name="generating-urls-by-action-name"></a>Gerando URLs pelo nome da ação

A [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e todas as sobrecargas relacionadas são projetadas para gerar o ponto de extremidade de destino especificando um nome de controlador e um nome de ação.

Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são fornecidos pelo tempo de execução:

* O valor de `controller` e `action` fazem parte de [valores de ambiente](#ambient) e valores. O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia a ação atual.

O roteamento tenta usar os valores em valores de ambiente para preencher as informações que não foram fornecidas durante a geração de uma URL. Considere uma rota como `{a}/{b}/{c}/{d}` com valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`:

* O roteamento tem informações suficientes para gerar uma URL sem valores adicionais.
* O roteamento tem informações suficientes porque todos os parâmetros de rota têm um valor.

Se o valor `{ d = Donovan }` for adicionado:

* O valor `{ d = David }` é ignorado.
* O caminho de URL gerado é `Alice/Bob/Carol/Donovan`.

**Aviso**: os caminhos de URL são hierárquicos. No exemplo anterior, se o valor `{ c = Cheryl }` for adicionado:

* Ambos os valores `{ c = Carol, d = David }` são ignorados.
* Não há mais um valor para `d` e a geração de URL falha.
* Os valores desejados de `c` e `d` devem ser especificados para gerar uma URL.  

Talvez você espere atingir esse problema com o `{controller}/{action}/{id?}`de rota padrão. Esse problema é raro na prática porque `Url.Action` sempre especifica explicitamente um `controller` e `action` valor.

Várias sobrecargas de [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) usam um objeto de valores de rota para fornecer valores para parâmetros de rota diferentes de `controller` e `action`. O objeto de valores de rota é usado frequentemente com `id`. Por exemplo, `Url.Action("Buy", "Products", new { id = 17 })`. O objeto de valores de rota:

* Por convenção geralmente é um objeto de tipo anônimo.
* Pode ser um `IDictionary<>` ou um [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

O código anterior gera `/Products/Buy/17?color=red`.

O código a seguir gera uma URL absoluta:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Para criar uma URL absoluta, use um dos seguintes:

* Uma sobrecarga que aceita uma `protocol`. Por exemplo, o código anterior.
* [LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que gera URIs absolutos por padrão.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Gerar URLs por rota

O código anterior demonstrou a geração de uma URL passando o nome do controlador e da ação. `IUrlHelper` também fornece a família de métodos [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) . Esses métodos são semelhantes a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), mas não copiam os valores atuais de `action` e `controller` para os valores de rota. O uso mais comum de `Url.RouteUrl`:

* Especifica um nome de rota para gerar a URL.
* Geralmente, não especifica um controlador ou um nome de ação.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

O seguinte arquivo Razor gera um link HTML para o `Destination_Route`:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Gerar URLs em HTML e Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> fornece os métodos de <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para gerar elementos `<form>` e `<a>` respectivamente. Esses métodos usam o método [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para gerar uma URL e aceitam argumentos semelhantes. O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.

TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`. Ambos usam `IUrlHelper` para sua implementação. Consulte [auxiliares de marca em formulários](xref:mvc/views/working-with-forms) para obter mais informações.

Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Geração de URL nos resultados da ação

Os exemplos anteriores mostraram o uso de `IUrlHelper` em um controlador. O uso mais comum em um controlador é gerar uma URL como parte de um resultado de ação.

As classes base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação. Um uso típico é redirecionar depois de aceitar a entrada do usuário:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

A ação resulta em métodos de fábrica, como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> seguir um padrão semelhante aos métodos no `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rotas convencionais dedicadas

O [Roteamento convencional](#cr) pode usar um tipo especial de definição de rota chamada de [rota convencional dedicada](#dcr). No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Usando as definições de rota anteriores, `Url.Action("Index", "Home")` gera o caminho da URL `/` usando a rota `default`, mas por quê? Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.

As [rotas convencionais dedicadas](#dcr) contam com um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impede que a rota seja muito [ávido](xref:fundamentals/routing#greedy) com a geração de URL. Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota. Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão. A geração de URL usando `blog` falha porque os valores `{ controller = Home, action = Index }` não correspondem `{ controller = Blog, action = Article }`. O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

As [áreas](xref:mvc/controllers/areas) são um recurso do MVC usado para organizar a funcionalidade relacionada em um grupo como separado:

* Namespace de roteamento para ações do controlador.
* Estrutura de pastas para exibições.

O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que eles tenham áreas diferentes. O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`. Esta seção discute como o roteamento interage com áreas. Consulte [áreas](xref:mvc/controllers/areas) para obter detalhes sobre como as áreas são usadas com exibições.

O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma rota de `area` para um `area` chamado `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> é chamado para criar o `"blog_route"`. O segundo parâmetro, `"Blog"`, é o nome da área.

Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a rota de `"blog_route"` gera os valores de rota `{ area = Blog, controller = Users, action = AddUser }`. O valor de rota `area` é produzido por um valor padrão para `area`. A rota criada por `MapAreaControllerRoute` é equivalente à seguinte:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`. O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.

O roteamento convencional é dependente da ordem. Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área.

Usando o exemplo anterior, os valores de rota `{ area = Blog, controller = Users, action = AddUser }` correspondem à seguinte ação:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

O atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) é o que denota um controlador como parte de uma área. Esse controlador está na área de `Blog`. Os controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** correspondem quando o valor de rota `area` é fornecido pelo roteamento. No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

O namespace de cada controlador é mostrado aqui para fins de integridade. Se os controladores anteriores usarem o mesmo namespace, um erro do compilador será gerado. Namespaces de classe não têm efeito sobre o roteamento do MVC.

Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`. O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.

<a name="aa"></a>

Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.

Ao executar uma ação dentro de uma área, o valor de rota para `area` está disponível como um [valor de ambiente](#ambient) para o roteamento a ser usado para a geração de URL. Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

O código a seguir gera uma URL para `/Zebra/Users/AddUser`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definição de ação

Os métodos públicos em um controlador, exceto aqueles com o atributo [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , são ações.

## <a name="sample-code"></a>Código de exemplo

 * O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.
* [Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O ASP.NET Core MVC usa o [middleware](xref:fundamentals/middleware/index) de Roteamento para fazer as correspondências das URLs de solicitações de entrada e mapeá-las para ações. As rotas são definidas em atributos ou no código de inicialização. Elas descrevem como deve ser feita a correspondência entre caminhos de URL e ações. As rotas também são usadas para gerar URLs (para links) enviados em resposta.

As ações são roteadas convencionalmente ou segundo os atributos. Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo. Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).

Este documento explicará as interações entre o MVC e o roteamento e como aplicativos MVC comuns usam recursos de roteamento. Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes sobre o roteamento avançado.

## <a name="setting-up-routing-middleware"></a>Configurando o middleware de Roteamento

No método *Configurar*, você poderá ver código semelhante a:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Dentro da chamada para `UseMvc`, `MapRoute` é usado para criar uma única rota, que chamaremos de rota `default`. A maioria dos aplicativos MVC usa uma rota com um modelo semelhante à rota `default`.

O modelo de rota `"{controller=Home}/{action=Index}/{id?}"` pode corresponder a um caminho de URL como `/Products/Details/5` e extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` gerando tokens para o caminho. O MVC tentará localizar um controlador chamado `ProductsController` e executar a ação `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Observe que, neste exemplo, o model binding usaria o valor de `id = 5` para definir o parâmetro `id` como `5` ao invocar essa ação. Consulte [Model binding](../models/model-binding.md) para obter mais detalhes.

Usando a rota `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

O modelo da rota:

* `{controller=Home}` define `Home` como o `controller` padrão

* `{action=Index}` define `Index` como o `action` padrão

* `{id?}` define `id` como opcional

Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência. Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.

`"{controller=Home}/{action=Index}/{id?}"` pode corresponder ao caminho da URL `/` e produzirá os valores de rota `{ controller = Home, action = Index }`. Os valores de `controller` e `action` usam os valores padrão, `id` não produz um valor, uma vez que não há nenhum segmento correspondente no caminho da URL. O MVC usaria esses valores de rota para selecionar a ação `HomeController` e `Index`:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Usando essa definição de controlador e modelo de rota, a ação `HomeController.Index` seria executada para qualquer um dos caminhos de URL a seguir:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

O método de conveniência `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Pode ser usado para substituir:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` e `UseMvcWithDefaultRoute` adicionam uma instância de `RouterMiddleware` ao pipeline de middleware. O MVC não interage diretamente com o middleware e usa o roteamento para tratar das solicitações. O MVC é conectado às rotas por meio de uma instância de `MvcRouteHandler`. O código dentro de `UseMvc` é semelhante ao seguinte:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` não define diretamente nenhuma rota, ele adiciona um espaço reservado à coleção de rotas para a rota `attribute`. A sobrecarga `UseMvc(Action<IRouteBuilder>)` permite adicionar suas próprias rotas e também dá suporte ao roteamento de atributos.  `UseMvc` e todas as suas variações adicionam um espaço reservado para o atributo roteamento de atributo de rota está sempre disponível, independentemente de como você configura `UseMvc`. `UseMvcWithDefaultRoute` define uma rota padrão e dá suporte ao roteamento de atributos. A seção [Roteamento de atributos](#attribute-routing-ref-label) inclui mais detalhes sobre o roteamento de atributos.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Roteamento convencional

A rota `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

O código anterior é um exemplo de roteamento convencional. Esse estilo é chamado de roteamento convencional porque ele estabelece uma *Convenção* para caminhos de URL:

* O primeiro segmento de caminho mapeia para o nome do controlador.
* O segundo mapeia para o nome da ação.
* O terceiro segmento é usado para um `id`opcional. `id` mapeia para uma entidade de modelo.

Usando essa rota `default`, o caminho da URL `/Products/List` é mapeado para a ação `ProductsController.List` e `/Blog/Article/17` é mapeado para `BlogController.Article`. Esse mapeamento é baseado **somente** nos nomes do controlador e da ação e não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.

> [!TIP]
> Usar o roteamento convencional com a rota padrão permite compilar o aplicativo rapidamente sem precisar criar um novo padrão de URL para cada ação que você definir. Para um aplicativo com ações de estilo CRUD, ter consistência para as URLs em seus controladores pode ajudar a simplificar seu código e a tornar sua interface do usuário mais previsível.

> [!WARNING]
> O `id` é definido como opcional pelo modelo de rota, o que significa que suas ações podem ser executadas sem a ID fornecida como parte da URL. Normalmente, o que acontecerá se `id` for omitido da URL é que ele será definido como `0` pelo model binding e, dessa forma, não será encontrada no banco de dados nenhuma entidade correspondente a `id == 0`. O roteamento de atributos pode lhe proporcionar controle refinado para tornar a ID obrigatória para algumas ações e não para outras. Por convenção, a documentação incluirá parâmetros opcionais, como `id`, quando for provável que eles apareçam no uso correto.

## <a name="multiple-routes"></a>Várias rotas

É possível adicionar várias rotas dentro de `UseMvc` adicionando mais chamadas para `MapRoute`. Fazer isso permite que você defina várias convenções ou que adicione rotas convencionais dedicadas a uma ação específica, como:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

A rota `blog` aqui é uma *rota convencional dedicada*, o que significa que ela usa o sistema de roteamento convencional, mas é dedicada a uma ação específica. Como `controller` e `action` não aparecem no modelo de rota como parâmetros, eles só podem ter os valores padrão e, portanto, essa rota sempre será mapeada para a ação `BlogController.Article`.

As rotas na coleção de rotas são ordenadas e serão processadas na ordem em que forem adicionadas. Portanto, neste exemplo, a rota `blog` será tentada antes da rota `default`.

> [!NOTE]
> *Rotas convencionais dedicadas* geralmente usam parâmetros de rota **catch-all** como `{*article}` para capturar a parte restante do caminho da URL. Isso pode fazer com que uma rota fique "muito ambiciosa", ou seja, que faça a correspondência com URLs que deveriam ser correspondidas com outras rotas. Coloque as rotas "ambiciosas" mais adiante na tabela de rotas para solucionar esse problema.

### <a name="fallback"></a>Fallback

Como parte do processamento de solicitações, o MVC verificará se o valores das rotas podem ser usados para encontrar um controlador e uma ação em seu aplicativo. Se os valores das rotas não corresponderem a uma ação, a rota não será considerada correspondente e a próxima rota será tentada. Isso é chamado de *fallback* e sua finalidade é simplificar casos em que rotas convencionais se sobrepõem.

### <a name="disambiguating-actions"></a>Desambiguação de ações

Quando duas ações correspondem por meio do roteamento, o MVC precisa resolver a ambiguidade para escolher a "melhor" candidata ou lançar uma exceção. Por exemplo:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Esse controlador define duas ações que fariam a correspondência entre caminho da URL `/Products/Edit/17` e a os dados da rota `{ controller = Products, action = Edit, id = 17 }`. Este é um padrão comum para controladores MVC em que `Edit(int)` mostra um formulário para editar um produto e `Edit(int, Product)` processa o formulário postado. Para que isso seja possível, o MVC precisa escolher `Edit(int, Product)` quando a solicitação é um `POST` HTTP e `Edit(int)` quando o verbo HTTP é qualquer outra coisa.

O `HttpPostAttribute` (`[HttpPost]`) é uma implementação de `IActionConstraint` que só permite que a ação seja selecionada quando o verbo HTTP é `POST`. A presença de um `IActionConstraint` faz do `Edit(int, Product)` uma "melhor" correspondência do que `Edit(int)`, portanto, `Edit(int, Product)` será tentado primeiro.

Você só precisará gravar implementações personalizadas de `IActionConstraint` em cenários especializados, mas é importante compreender a função de atributos como `HttpPostAttribute` – atributos semelhantes são definidos para outros verbos HTTP. No roteamento convencional, é comum que ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de `show form -> submit form`. A conveniência desse padrão ficará mais aparente após você revisar a seção [Noções básicas sobre IActionConstraint](#understanding-iactionconstraint).

Se várias rotas corresponderem e o MVC não puder encontrar uma rota "melhor", ele gerará um `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Nomes de rotas

As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Os nomes de rotas dão a uma rota um nome lógico, de modo que a rota nomeada possa ser usada para geração de URL. Isso simplifica muito a criação de URLs quando a ordenação de rotas poderia complicá-la. Nomes de rotas devem ser exclusivos no nível do aplicativo.

Os nomes de rotas não têm impacto sobre a correspondência de URLs ou o tratamento de solicitações; eles são usados apenas para a geração de URLs. [Roteamento](xref:fundamentals/routing) tem informações mais detalhadas sobre geração de URLs, incluindo a geração de URLs em auxiliares específicos do MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Roteamento de atributo

O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota. No exemplo a seguir, `app.UseMvc();` é usado no método `Configure` e nenhuma rota é passada. O `HomeController` corresponderá a um conjunto de URLs semelhantes ao que a rota padrão `{controller=Home}/{action=Index}/{id?}` corresponderia:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

A ação `HomeController.Index()` será executada para qualquer um dos caminhos de URL `/`, `/Home` ou `/Home/Index`.

> [!NOTE]
> Este exemplo destaca uma diferença importante de programação entre o roteamento de atributo e o roteamento convencional. O roteamento de atributo requer mais entradas para especificar uma rota; a rota padrão convencional manipula as rotas de forma mais sucinta. No entanto, o roteamento de atributo permite (e exige) o controle preciso de quais modelos de rota se aplicam a cada ação.

Com o roteamento de atributo, o nome do controlador e os nomes de ação não desempenham **nenhuma** função quanto a qual ação é selecionada. Este exemplo corresponderá as mesmas URLs que o exemplo anterior.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Os modelos de rota acima não definem parâmetros de rota para `action`, `area` e `controller`. Na verdade, esses parâmetros de rota não são permitidos em rotas de atributo. Uma vez que o modelo de rota já está associado a uma ação, não faria sentido analisar o nome da ação da URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Roteamento de atributo com atributos Http[Verb]

O roteamento de atributo também pode usar atributos `Http[Verb]`, como `HttpPostAttribute`. Todos esses atributos podem aceitar um modelo de rota. Este exemplo mostra duas ações que correspondem ao mesmo modelo de rota:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Para um caminho de URL como `/products`, a ação `ProductsApi.ListProducts` será executada quando o verbo HTTP for `GET` e `ProductsApi.CreateProduct` será executado quando o verbo HTTP for `POST`. Primeiro, o roteamento de atributo faz a correspondência da URL com o conjunto de modelos de rota definidos por atributos de rota. Quando um modelo de rota for correspondente, restrições de `IActionConstraint` serão aplicadas para determinar quais ações podem ser executadas.

> [!TIP]
> Ao criar uma API REST, é raro que você queira usar `[Route(...)]` em um método de ação, pois a ação aceitará todos os métodos HTTP. É melhor usar o `Http*Verb*Attributes` mais específico para ser preciso quanto ao que tem suporte de sua API. Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.

Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota. Neste exemplo, `id` é obrigatório como parte do caminho da URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

A ação `ProductsApi.GetProduct(int)` será executada para um caminho de URL como `/products/3`, mas não para um caminho de URL como `/products`. Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.

## <a name="route-name"></a>Nome da rota

O código a seguir define um *nome da rota* como `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica. Nomes de rota não têm impacto sobre o comportamento de correspondência da URL e são usados somente para geração de URLs. Nomes de rotas devem ser exclusivos no nível do aplicativo.

> [!NOTE]
> Compare isso com a *rota padrão* convencional, que define o parâmetro `id` como opcional (`{id?}`). Essa capacidade de especificar APIs de forma específica tem vantagens, como permitir que `/products` e `/products/5` sejam expedidos para ações diferentes.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Combinando rotas

Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais. Modelos de rota definidos no controlador precedem modelos de rota nas ações. Colocar um atributo de rota no controlador faz com que  **todas** as ações no controlador usem o roteamento de atributo.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

Neste exemplo, o caminho de URL `/products` pode corresponder a `ProductsApi.ListProducts` e o caminho de URL `/products/5` pode corresponder a `ProductsApi.GetProduct(int)`. Ambas as ações correspondem somente a `GET` HTTP porque elas são marcadas com o `HttpGetAttribute`.

Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador. Este exemplo corresponde a um conjunto de caminhos de URL semelhante à *rota padrão*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordenando rotas de atributos

Ao contrário das rotas convencionais, que são executadas em uma ordem definida, o roteamento de atributo cria uma árvore e corresponde a todas as rotas simultaneamente. O comportamento é como se as entradas de rota fossem colocadas em uma ordem ideal; as rotas mais específicas têm uma chance de ser executadas antes das rotas mais gerais.

Por exemplo, uma rota como `blog/search/{topic}` é mais específica que uma rota como `blog/{*article}`. Em termos da lógica, a rota `blog/search/{topic}` é "executada" primeiro, por padrão, porque essa é a única ordem que faz sentido. Usando o roteamento convencional, o desenvolvedor é responsável por colocar as rotas na ordem desejada.

Rotas de atributos podem configurar uma ordem, usando a propriedade `Order` de todos os atributos de rota fornecidos pela estrutura. As rotas são processadas segundo uma classificação crescente da propriedade `Order`. A ordem padrão é `0`. Uma rota definida usando `Order = -1` será executada antes de rotas que não definem uma ordem. Uma rota definida usando `Order = 1` será executada após a ordem das rotas padrão.

> [!TIP]
> Evite depender de `Order`. Se o seu espaço de URL exigir valores de ordem explícita para fazer o roteamento corretamente, provavelmente ele também será confuso para os clientes. De modo geral, o roteamento de atributos selecionará a rota correta com a correspondência de URL. Se a ordem padrão usada para a geração de URL não estiver funcionando, usar o nome da rota como uma substituição geralmente será mais simples do que aplicar a propriedade `Order`.

Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação. Informações sobre a ordem de rota nos tópicos do Razor Pages estão disponíveis em [Convenções de rota e aplicativo do Razor Pages: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Substituição de token em modelos de rota ([controlador] [ação] [área])

Para conveniência, as rotas de atributo dão suporte à *substituição de token* colocando um token entre chaves quadradas (`[`, `]`). Os tokens `[action]`, `[area]` e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida. No exemplo a seguir, as ações correspondem a caminhos de URL conforme descrito nos comentários:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

A substituição de token ocorre como a última etapa da criação das rotas de atributo. O exemplo acima se comportará da mesma forma que o código a seguir:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Rotas de atributo também podem ser combinadas com herança. Isso é especialmente eficiente em combinação com a substituição de token.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

A substituição de token também se aplica a nomes de rota definidos por rotas de atributo. `[Route("[controller]/[action]", Name="[controller]_[action]")]` gera um nome de rota exclusivo para cada ação.

Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Usar um transformador de parâmetro para personalizar a substituição de token

A substituição do token pode ser personalizada usando um transformador de parâmetro. Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros. Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.

O `RouteTokenTransformerConvention` é uma convenção de modelo de aplicativo que:

* Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.
* Personaliza os valores de token de rota de atributo conforme eles são substituídos.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Várias rotas

O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação. O uso mais comum desse recurso é para simular o comportamento da *rota convencional padrão*, conforme mostrado no exemplo a seguir:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Colocar vários atributos de rota no controlador significa que cada um deles será combinado com cada um dos atributos de rota nos métodos de ação.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Quando vários atributos de rota (que implementam `IActionConstraint`) são colocados em uma ação, cada restrição da ação combina com o modelo de rota do atributo que a definiu.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Embora o uso de várias rotas em ações possa parecer eficaz, é melhor manter o espaço de URL de seu aplicativo simples e bem definido. Use várias rotas em ações somente quando for necessário; por exemplo, para dar suporte a clientes existentes.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo

Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Atributos de rota personalizados usando `IRouteTemplateProvider`

Todos os atributos de rota fornecidos na estrutura ( `[Route(...)]`, `[HttpGet(...)]` etc.) implementam a interface `IRouteTemplateProvider`. O MVC procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado e usa aqueles que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.

Você pode implementar `IRouteTemplateProvider` para definir seus próprios atributos de rota. Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

O atributo do exemplo acima configura automaticamente o `Template` como `"api/[controller]"` quando `[MyApiController]` é aplicado.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Usando o Modelo de Aplicativo para personalizar rotas de atributo

O *modelo de aplicativo* é um modelo de objeto criado durante a inicialização com todos os metadados usados pelo MVC para rotear e executar suas ações. O *modelo de aplicativo* inclui todos os dados reunidos dos atributos de rota (por meio de `IRouteTemplateProvider`). Você pode escrever *convenções* para modificar o modelo do aplicativo no momento da inicialização para personalizar o comportamento do roteamento. Esta seção mostra um exemplo simples de personalização de roteamento usando o modelo de aplicativo.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Roteamento misto: roteamento de atributo versus roteamento convencional

Aplicativos MVC podem combinar o uso do roteamento convencional e do roteamento de atributo. É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.

As ações são roteadas convencionalmente ou segundo os atributos. Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo. Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa. **Qualquer** atributo de rota no controlador faz com que todas as ações no atributo de controlador sejam roteadas.

> [!NOTE]
> O que diferencia os dois tipos de sistemas de roteamento é o processo aplicado após uma URL corresponder a um modelo de rota. No roteamento convencional, os valores de rota da correspondência são usados para escolher a ação e o controlador em uma tabela de pesquisa com todas as ações roteadas convencionais. No roteamento de atributo, cada modelo já está associado a uma ação e nenhuma pesquisa adicional é necessária.

## <a name="complex-segments"></a>Segmentos complexos

Segmentos complexos (por exemplo, `[Route("/dog{token}cat")]`), são processados combinando literais da direita para a esquerda de uma maneira não Greedy. Veja [o código-fonte](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) para obter uma descrição. Para obter mais informações, confira [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Geração de URL

Aplicativos MVC podem usar os recursos de geração de URL do roteamento para gerar links de URL para ações. Gerar URLs elimina a necessidade de codificar URLs, tornando seu código mais robusto e sustentável. Esta seção tem como foco os recursos de geração de URL fornecidos pelo MVC e só aborda as noções básicas de como a geração de URL funciona. Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.

A interface `IUrlHelper` é a parte subjacente da infraestrutura entre o MVC e o roteamento para geração de URL. Você encontrará uma instância de `IUrlHelper` disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.

Neste exemplo, a interface `IUrlHelper` é usada por meio a propriedade `Controller.Url` para gerar uma URL para outra ação.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho de URL `/UrlGeneration/Destination`. Esse caminho de URL é criado pelo roteamento combinando os valores de rota da solicitação atual (valores de ambiente) com os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente. Um parâmetro de rota que não tem um valor pode usar um valor padrão se houver um ou pode ser ignorado se for opcional (como no caso de `id` neste exemplo). A geração de URL falhará se qualquer parâmetro de rota obrigatório não tiver um valor correspondente. Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.

O exemplo de `Url.Action` acima pressupõe que o roteamento seja convencional, mas a geração de URL funciona de forma semelhante com o roteamento de atributo, embora os conceitos sejam diferentes. Com o roteamento convencional, os valores de rota são usados para expandir um modelo e os valores de rota para `controller` e `action` normalmente são exibidos no modelo – isso funciona porque as URLs correspondidas pelo roteamento aderem a uma *convenção*. No roteamento de atributo, os valores de rota para `controller` e `action` não podem ser exibidos no modelo; em vez disso, eles são usados para pesquisar o modelo a ser usado.

Este exemplo usa o roteamento de atributo:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

O MVC cria uma tabela de pesquisa de todas as ações de atributo roteadas e faz a correspondência dos valores de `controller` e `action` para selecionar o modelo de rota a ser usado para geração de URL. Na amostra acima, `custom/url/to/destination` é gerado.

### <a name="generating-urls-by-action-name"></a>Gerando URLs pelo nome da ação

`Url.Action` (`IUrlHelper` . `Action`) e todas as sobrecargas relacionadas são baseadas na ideia de que você deseja especificar ao que está vinculando, especificando um nome do controlador e um nome da ação.

> [!NOTE]
> Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são especificados para você-o valor de `controller` e `action` são parte dos *valores de ambiente* **e** *valores*. O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.

O roteamento tenta usar os valores em valores de ambiente para preencher informações que você não forneceu ao gerar uma URL. Usando uma rota como `{a}/{b}/{c}/{d}` e valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, o roteamento tem informações suficientes para gerar uma URL sem valores adicionais – uma vez que todos os parâmetros de rota têm um valor. Se você tiver adicionado o valor `{ d = Donovan }`, o valor `{ d = David }` será ignorado e o caminho de URL gerado será `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Caminhos de URL são hierárquicos. No exemplo acima, se você tiver adicionado o valor `{ c = Cheryl }`, ambos os valores `{ c = Carol, d = David }` serão ignorados. Nesse caso, não teremos mais um valor para `d` e a geração de URL falhará. Você precisaria especificar o valor desejado de `c` e `d`.  Você pode esperar se deparar com esse problema com a rota padrão (`{controller}/{action}/{id?}`) – mas raramente encontrará esse comportamento na prática, pois `Url.Action` sempre especificará explicitamente um valor de `controller` e `action`.

Sobrecargas maiores de `Url.Action` também usam um objeto adicional de *valores de rota* para fornecer valores para parâmetros de rota diferentes de `controller` e `action`. É mais comum ver isso com `id` como `Url.Action("Buy", "Products", new { id = 17 })`. Por convenção, o objeto de *valores de rota* geralmente é um objeto de tipo anônimo, mas também pode ser um `IDictionary<>` ou um *objeto .NET simples*. Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Para criar uma URL absoluta, use uma sobrecarga que aceita um `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Gerando URLs pela rota

O código acima demonstrou a geração de uma URL passando o nome do controlador e da ação. `IUrlHelper` também fornece a família de métodos `Url.RouteUrl`. Esses métodos são semelhantes a `Url.Action`, mas não copiam os valores atuais de `action` e `controller` para os valores de rota. O uso mais comum é especificar um nome de rota para usar uma rota específica para gerar a URL, geralmente *sem* especificar um nome de controlador ou de ação.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Gerar URLs em HTML

`IHtmlHelper` fornece o métodos `HtmlHelper` e `Html.BeginForm` de `Html.ActionLink` para gerar elementos `<form>` e `<a>` respectivamente. Esses métodos usam o método `Url.Action` para gerar uma URL e aceitam argumentos semelhantes. O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.

TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`. Ambos usam `IUrlHelper` para sua implementação. Consulte [Trabalhando com Formulários](../views/working-with-forms.md) para obter mais informações.

Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Gerando URLS nos resultados da ação

Os exemplos acima mostraram o uso de `IUrlHelper` em um controlador, enquanto o uso mais comum em um controlador é gerar uma URL como parte do resultado de uma ação.

As classes base `ControllerBase` e `Controller` fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação. Um uso típico é para redirecionar após aceitar a entrada do usuário.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Os métodos de fábrica dos resultados da ação seguem um padrão semelhante aos métodos em `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Caso especial para rotas convencionais dedicadas

O roteamento convencional pode usar um tipo especial de definição de rota chamado *rota convencional dedicada*. No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Usando essas definições de rota, `Url.Action("Index", "Home")` gerará o caminho de URL `/` com a rota `default`, mas por quê? Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.

Rotas convencionais dedicadas dependem de um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impeça que a rota seja "muito ambiciosa" com a geração de URLs. Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota. Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão. A geração de URL usando `blog` falhará porque os valores de `{ controller = Home, action = Index }` não correspondem a `{ controller = Blog, action = Article }`. O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Áreas

[Áreas](areas.md) são um recurso do MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace de roteamento (para ações do controlador) e estrutura de pasta (para exibições) separada. O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que tenham *áreas* diferentes. O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`. Esta seção aborda como o roteamento interage com as áreas. Consulte [Áreas](areas.md) para obter detalhes sobre como as áreas são usadas com exibições.

O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma *rota de área* para uma área chamada `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a primeira rota produzirá os valores de rota `{ area = Blog, controller = Users, action = AddUser }`. O valor de rota `area` é produzido por um valor padrão para `area`. De fato, a rota criada por `MapAreaRoute` é equivalente à seguinte:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`. O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.

> [!TIP]
> O roteamento convencional é dependente da ordem. De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.

Usando o exemplo acima, os valores de rota corresponderiam à ação a seguir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

O `AreaAttribute` é o que indica que um controlador faz parte de uma área; dizemos que esse controlador está na área `Blog`. Controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** corresponderão quando o valor de rota `area` for fornecido pelo roteamento. No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> O namespace de cada controlador é mostrado aqui para fins de integridade – caso contrário, os controladores teriam um conflito de nomenclatura e gerariam um erro do compilador. Namespaces de classe não têm efeito sobre o roteamento do MVC.

Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`. O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.

> [!NOTE]
> Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.

Ao executar uma ação dentro de uma área, o valor de rota para `area` estará disponível como um *valor de ambiente* para o roteamento usar para geração de URL. Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Entendendo IActionConstraint

> [!NOTE]
> Esta seção é uma análise aprofundada dos elementos internos da estrutura e de como o MVC escolhe uma ação para ser executada. Um aplicativo típico não precisará de um `IActionConstraint` personalizado

Provavelmente, você já usou `IActionConstraint` mesmo que não esteja familiarizado com a interface. O atributo `[HttpGet]` e atributos `[Http-VERB]` semelhantes implementam `IActionConstraint` para limitar a execução de um método de ação.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Presumindo a rota convencional padrão, o caminho de URL `/Products/Edit` produziria os valores `{ controller = Products, action = Edit }`, que corresponderiam a **ambas** as ações mostradas aqui. Na terminologia `IActionConstraint`, diríamos que essas duas ações são consideradas candidatas – uma vez que ambas correspondem aos dados da rota.

Quando for executado, `HttpGetAttribute` indicará que *Edit()* corresponde a *GET* e não corresponde a nenhum outro verbo HTTP. A ação `Edit(...)` não tem restrições definidas e, portanto, corresponderá a qualquer verbo HTTP. Sendo assim, supondo um `POST`, `Edit(...)` será correspondente. Mas, para um `GET`, ambas as ações ainda podem corresponder – no entanto, uma ação com um `IActionConstraint` sempre é considerada *melhor* que uma ação sem. Assim, como `Edit()` tem `[HttpGet]`, ela é considerada mais específica e será selecionada se as duas ações puderem corresponder.

Conceitualmente, `IActionConstraint` é uma forma de *sobrecarga*, mas em vez de uma sobrecarga de métodos com o mesmo nome, trata-se da sobrecarga entre ações que correspondem à mesma URL. O roteamento de atributo também usa `IActionConstraint` e pode fazer com que ações de controladores diferentes sejam consideradas candidatas.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementando IActionConstraint

A maneira mais simples de implementar um `IActionConstraint` é criar uma classe derivada de `System.Attribute` e colocá-la em suas ações e controladores. O MVC descobrirá automaticamente qualquer `IActionConstraint` que for aplicado como atributo. Você pode usar o modelo de aplicativo para aplicar restrições e, provavelmente, essa é a abordagem mais flexível, pois permite a você faça uma metaprogramação de como elas são aplicadas.

No exemplo a seguir, uma restrição escolhe uma ação com base em um *código de país* dos dados de rota. O [exemplo completo no GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Você é responsável por implementar o método `Accept` e por escolher uma "ordem" na qual a restrição deve ser executada. Nesse caso, o método `Accept` retorna `true` para indicar que a ação é correspondente quando o valor de rota `country` é correspondente. Isso é diferente de um `RouteValueAttribute`, pois permite o fallback para uma ação não atribuída. O exemplo mostra que se você definir uma ação `en-US`, um código de país como `fr-FR` fará o fallback para um controlador mais genérico que não tem `[CountrySpecific(...)]` aplicado.

A propriedade `Order` decide de qual *estágio* a restrição faz parte. Restrições de ação são executadas em grupos com base no `Order`. Por exemplo, todos atributos de método HTTP fornecidos pela estrutura usam o mesmo valor de `Order` para que sejam executados no mesmo estágio. Você pode ter tantos estágios quantos forem necessários para implementar suas políticas desejadas.

> [!TIP]
> Para decidir o valor para `Order`, considere se sua restrição deve ou não deve ser aplicada antes de métodos HTTP. Números inferiores são executados primeiro.

::: moniker-end
