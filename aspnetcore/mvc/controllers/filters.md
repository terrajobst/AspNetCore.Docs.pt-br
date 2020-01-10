---
title: Filtros no ASP.NET Core
author: Rick-Anderson
description: Saiba como os filtros funcionam e como usá-los no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 1/1/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 759c150e7f35f3f6a52947edc5ef41448dc227fe
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828965"
---
# <a name="filters-in-aspnet-core"></a>Filtros no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)

Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.

O filtros internos lidam com tarefas como:

* Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).
* Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).

É possível criar filtros personalizados para lidar com interesses paralelos. Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.  Filtros evitam a duplicação do código. Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.

Este documento se aplica ao Razor Pages, a controladores de API e controladores com exibição.

[Exibir ou baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Como os filtros funcionam

Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.  O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.

![A solicitação é processada por meio de outro middleware, middleware de roteamento, seleção de ação e o pipeline de invocação de ação. O processamento de solicitações continua por meio da Seleção de Ação, do Middleware de Roteamento e de diversos Outros Middlewares antes de se tornar uma resposta enviada ao cliente.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Tipos de filtro

Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:

* [Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação. Os filtros de autorização de circuito curto do pipeline se a solicitação não for autorizada.

* [Filtros de recurso](#resource-filters):

  * Execute após a autorização.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> executa o código antes do resto do pipeline de filtro. Por exemplo, `OnResourceExecuting` executa o código antes da Associação de modelo.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> executa o código após a conclusão do restante do pipeline.

* [Filtros de ação](#action-filters):

  * Execute o código imediatamente antes e depois que um método de ação for chamado.
  * Pode alterar os argumentos passados para uma ação.
  * Pode alterar o resultado retornado da ação.
  * **Não** têm suporte no Razor Pages.

* Os [filtros de exceção](#exception-filters) aplicam políticas globais a exceções sem tratamento que ocorrem antes da gravação do corpo da resposta.

* Os [filtros de resultado](#result-filters) executam o código imediatamente antes e após a execução dos resultados da ação. Eles são executados somente quando o método de ação é executado com êxito. Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.

O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado. Na saída, a solicitação é processada somente por Filtros de resultado e Filtros de recurso antes de se tornar uma resposta enviada ao cliente.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementação

Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.

Os filtros síncronos executam o código antes e depois do estágio do pipeline. Por exemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> é chamado antes que o método de ação seja chamado. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> é chamado após o método de ação retornar.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`. Por exemplo <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.

### <a name="multiple-filter-stages"></a>Vários estágios do filtro

É possível implementar interfaces para vários estágios do filtro em uma única classe. Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:

* Síncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* Assíncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas. Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama. Caso contrário, ela chama os métodos da interface síncrona. Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado. Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.

### <a name="built-in-filter-attributes"></a>Atributos de filtro internos

O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados. Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima. Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Use uma ferramenta como as [ferramentas de desenvolvedor do navegador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar os cabeçalhos. Em **cabeçalhos de resposta**, `author: Rick Anderson` é exibido.

O código a seguir implementa um `ActionFilterAttribute` que:

* Lê o título e o nome do sistema de configuração. Ao contrário do exemplo anterior, o código a seguir não exige que os parâmetros de filtro sejam adicionados ao código.
* Adiciona o título e o nome ao cabeçalho de resposta.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

As opções de configuração são fornecidas no [sistema de configuração](xref:fundamentals/configuration/index) usando o [padrão de opções](xref:fundamentals/configuration/options). Por exemplo, no arquivo *appSettings. JSON* :

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

No `StartUp.ConfigureServices`:

* A classe `PositionOptions` é adicionada ao contêiner de serviço com a área de configuração `"Position"`.
* O `MyActionFilterAttribute` é adicionado ao contêiner de serviço.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

O código a seguir mostra a classe `PositionOptions`:

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

O código a seguir aplica o `MyActionFilterAttribute` ao método `Index2`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

Em **cabeçalhos de resposta**, `author: Rick Anderson`e `Editor: Joe Smith` são exibidos quando o ponto de extremidade `Sample/Index2` é chamado.

O código a seguir aplica o `MyActionFilterAttribute` e o `AddHeaderAttribute` à página Razor:

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

Os filtros não podem ser aplicados aos métodos do manipulador de páginas do Razor. Eles podem ser aplicados ao modelo de página do Razor ou globalmente.

Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.

Atributos de filtro:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Escopos e ordem de execução dos filtros

Um filtro pode ser adicionado ao pipeline com um de três *escopos*:

* Usando um atributo em uma ação do controlador. Atributos de filtro não podem ser aplicados a métodos de manipuladores de Razor Pages.
* Usando um atributo em um controlador ou em uma página Razor.
* Globalmente para todos os controladores, ações e Razor Pages, conforme mostrado no código a seguir:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Ordem padrão de execução

Quando há vários filtros para um determinado estágio do pipeline, o escopo determina a ordem padrão de execução dos filtros.  Filtros globais circundam filtros de classe, que, por sua vez, circundam filtros de método.

Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*. A sequência de filtro:

* O código *anterior* dos filtros globais.
  * O código *anterior* dos filtros de página do controlador e do Razor.
    * O código *anterior* dos filtros de método de ação.
    * O código *posterior* dos filtros de método de ação.
  * O código *After* dos filtros de página do controlador e do Razor.
* O código *posterior* dos filtros globais.
  
O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.

| Sequence | Escopo do filtro | Método Filter |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controlador ou página Razor| `OnActionExecuting` |
| 3 | Método | `OnActionExecuting` |
| 4 | Método | `OnActionExecuted` |
| 5 | Controlador ou página Razor | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Filtros de nível de controlador

Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Estes métodos:

* Encapsulam os filtros executados para uma determinada ação.
* `OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.
* `OnActionExecuted` é chamado após todos os filtros da ação.
* `OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação. O código no filtro após `next` é executado após o método de ação.

Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.

O `TestController`:

* Aplica o `SampleActionFilterAttribute` (`[SampleActionFilter]`) à ação `FilterTest2`.
* Substitui `OnActionExecuting` e `OnActionExecuted`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

Navegar até `https://localhost:5001/Test2/FilterTest2` executa o seguinte código:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Filtros de nível de controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue`. Os filtros de nível de controlador **não** podem ser definidos para execução após os filtros aplicados aos métodos. A ordem é explicada na próxima seção.

Para Razor Pages, confira [Implementar filtros do Razor Page substituindo os métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Substituindo a ordem padrão

É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. `IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução. Um filtro com um valor de `Order` menor:

* Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.
* Executa o código *posterior* após um filtro com um valor mais alto de `Order`.

A propriedade `Order` é definida com um parâmetro de construtor:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Considere os dois filtros de ação no controlador a seguir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

Um filtro global é adicionado no `StartUp.ConfigureServices`:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

Os 3 filtros são executados na seguinte ordem:

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados. Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar. Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0. Como mencionado anteriormente, os filtros no nível do controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue` para filtros internos, o escopo determina a ordem, a menos que `Order` esteja definido como um valor diferente de zero.

No código anterior, `MySampleActionFilter` tem escopo global para que ele seja executado antes `MyAction2FilterAttribute`, que tem o escopo do controlador. Para fazer `MyAction2FilterAttribute` executar primeiro, defina o pedido como `int.MinValue`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Para fazer com que o filtro global `MySampleActionFilter` executado primeiro, defina `Order` como `int.MinValue`:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>Cancelamento e curto-circuito

O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro. Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`. O `ShortCircuitingResourceFilter`:

* É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.
* Causa um curto-circuito no restante do pipeline.

Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`. Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro. O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a>Injeção de dependência

Filtros podem ser adicionados por tipo ou por instância. Se você adicionar uma instância, ela será usada para cada solicitação. Se um tipo for adicionado, ele será ativado pelo tipo. Um filtro ativado por tipo significa:

* Uma instância é criada para cada solicitação.
* Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).

Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)). Dependências de construtor não podem ser fornecidas pela DI porque:

* Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados. 
* Essa é uma limitação do funcionamento dos atributos.

Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.

Os filtros anteriores podem ser aplicados a um método de ação ou controlador:

Os agentes estão disponíveis na DI. No entanto, evite criar e usar filtros apenas para fins de registro em log. O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log. Registro em log adicionado aos filtros:

* Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.
* **Não** deve registrar ações ou outros eventos da estrutura. Os filtros internos registram ações de log e eventos de estrutura.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`. Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.

O código a seguir mostra `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada. O runtime do ASP.NET Core não garante:

  * Que uma única instância do filtro será criada.
  * Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.

* Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carrega o tipo especificado na DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI. Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:

* Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.  Eles têm suas dependências atendidas pelo contêiner de DI.
* Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.

Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada. O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.

* Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.

O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtros de autorização

Filtros de autorização:

* São os primeiros filtros executados no pipeline de filtro.
* Controlam o acesso aos métodos de ação.
* Têm um método anterior, mas não têm um método posterior.

Filtros de autorização personalizados exigem uma estrutura de autorização personalizada. Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado. O filtro de autorização interno:

* Chama o sistema de autorização.
* Não autoriza solicitações.

**Não** gera exceções dentro de filtros de autorização:

* A exceção não será tratada.
* Os filtros de exceção não tratarão a exceção.

Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.

Saiba mais sobre [Autorização](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtros de recurso

Filtros de recurso:

* Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* A execução encapsula grande parte do pipeline de filtro.
* Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.

Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline. Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.

Exemplos de filtros de recurso:

* [O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Impede o model binding de acessar os dados do formulário.
  * Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.

## <a name="action-filters"></a>Filtros de ação

Os filtros de ação **não** se aplicam ao Razor Pages. O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>. Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).

Filtros de ação:

* Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* A execução deles envolve a execução de métodos de ação.

O código a seguir mostra um exemplo de filtro de ação:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-permite ler as entradas para um método de ação.
* <xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.

Gerar uma exceção em um método de ação:

* Impede a execução de filtros subsequentes.
* Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.

A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção. Definir essa propriedade como nulo:

  * Trata efetivamente a exceção.
  * `Result` é executado como se tivesse retornado do método de ação.

Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Executa todos os próximos filtros de ação e o método de ação.
* Retorna `ActionExecutedContext`.

Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).

A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.

O filtro de ação `OnActionExecuting` pode ser usado para:

* Validar o estado do modelo.
* Retornar um erro se o estado for inválido.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

O método `OnActionExecuted` é executado após o método de ação:

* E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção. Definindo `Exception` como nulo:

  * Trata efetivamente uma exceção.
  * `ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtros de exceção

Filtros de exceção:

* Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.
* Podem ser usados para implementar políticas de tratamento de erro comuns.

O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

O código a seguir testa o filtro de exceção:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Filtros de exceção:

* Não têm eventos anteriores nem posteriores.
* Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Tratam as exceções sem tratamento que ocorrem no Razor Page ou na criação do controlador, no [model binding](xref:mvc/models/model-binding), nos filtros de ação ou nos métodos de ação.
* **Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.

Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta. Isso interrompe a propagação da exceção. Um filtro de exceção não pode transformar uma exceção em "êxito". Somente um filtro de ação pode fazer isso.

Filtros de exceção:

* São bons para interceptar as exceções que ocorrem nas ações.
* Não são tão flexíveis quanto o middleware de tratamento de erro.

Prefira o middleware para tratamento de exceção. Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado. Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML. Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.

## <a name="result-filters"></a>Filtros de resultado

Filtros de resultado:

* Implemente uma interface:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* A execução deles envolve a execução de resultados de ação.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter e IAsyncResultFilter

O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

O tipo de resultado que está sendo executado depende da ação. Uma ação que retorna uma exibição inclui todo o processamento do Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> que está sendo executado. Um método de API pode executar alguma serialização como parte da execução do resultado. Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).

Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação. Os filtros de resultado não são executados quando:

* Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.
* Um filtro de exceção trata uma exceção produzindo um resultado de ação.

O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`. Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia. Gerando uma exceção no `IResultFilter.OnResultExecuting`:

* Impede a execução do resultado da ação e dos filtros subsequentes.
* É tratado como uma falha em vez de um resultado bem-sucedido.

Quando o método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> é executado, a resposta provavelmente já foi enviada ao cliente. Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada.

`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.

`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção. A definição de `Exception` como NULL trata efetivamente de uma exceção e impede que a exceção seja gerada novamente mais tarde no pipeline. Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado. Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.

Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação. Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse. A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter

As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação. Isso inclui resultados de ação produzidos por:

* Filtros de autorização e filtros de recursos que curto-Circuit.
* Filtros de exceção.

Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro. Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`. Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada. Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.

Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

O filtro é aplicado no código a seguir:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Teste o código anterior executando o [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):

* Invocar as ferramentas de desenvolvedor F12.
* Navegue para `https://localhost:5001/Sample/HeaderWithFactory`.

As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:

* **autor:** `Rick Anderson`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **interno:** `My header`

O código anterior cria o cabeçalho de resposta **interno:** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implementado em um atributo

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filtros que implementam `IFilterFactory` são úteis para filtros que:

* Não exigem a passagem de parâmetros.
* Tenha dependências de construtor que precisem ser atendidas pela DI.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Usando middleware no pipeline de filtros

Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline. Mas os filtros diferem do middleware, pois fazem parte do tempo de execução, o que significa que eles têm acesso ao contexto e às construções.

Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros. O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.

## <a name="next-actions"></a>Próximas ações

* Consulte [métodos de filtro para Razor Pages](xref:razor-pages/filter).
* Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)

Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.

O filtros internos lidam com tarefas como:

* Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).
* Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).

É possível criar filtros personalizados para lidar com interesses paralelos. Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.  Filtros evitam a duplicação do código. Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.

Este documento se aplica ao Razor Pages, a controladores de API e controladores com exibição.

[Exibir ou baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Como os filtros funcionam

Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.  O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.

![A solicitação é processada por meio de Outro Middleware, do Middleware de Roteamento, da Seleção de Ação e do Pipeline de Invocação de Ações do ASP.NET Core. O processamento de solicitações continua por meio da Seleção de Ação, do Middleware de Roteamento e de diversos Outros Middlewares antes de se tornar uma resposta enviada ao cliente.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Tipos de filtro

Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:

* [Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação. Os filtros de autorização podem causar um curto-circuito do pipeline se uma solicitação não for autorizada.

* [Filtros de recurso](#resource-filters):

  * Execute após a autorização.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> pode executar o código antes do restante do pipeline de filtros. Por exemplo, `OnResourceExecuting` pode executar o código antes do model binding.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> pode executar o código após a conclusão do restante do pipeline.

* [Filtros de ação](#action-filters) podem executar código imediatamente antes e depois de um método de ação individual ser chamado. Eles podem ser usados para manipular os argumentos passados para uma ação, bem como o resultado da ação. Os filtros de ação **não** são compatíveis com o Razor Pages.

* [Filtros de exceção](#exception-filters) são usados para aplicar políticas globais para exceções sem tratamento que ocorrem antes que qualquer coisa tenha sido gravada no corpo da resposta.

* [Filtros de resposta](#result-filters) podem executar código imediatamente antes e depois da execução de resultados de ações individuais. Eles são executados somente quando o método de ação é executado com êxito. Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.

O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado. Na saída, a solicitação é processada somente por Filtros de resultado e Filtros de recurso antes de se tornar uma resposta enviada ao cliente.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementação

Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.

Os filtros síncronos podem executar o código antes (`On-Stage-Executing`) e depois (`On-Stage-Executed`) do estágio de pipeline. Por exemplo, `OnActionExecuting` é chamado antes que o método de ação seja chamado. `OnActionExecuted` é chamado após o método de ação retornar.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.  Cada um dos métodos `On-Stage-ExecutionAsync` usa um `FilterType-ExecutionDelegate` que executa o estágio de pipeline do filtro.

### <a name="multiple-filter-stages"></a>Vários estágios do filtro

É possível implementar interfaces para vários estágios do filtro em uma única classe. Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e os respectivos equivalentes assíncronos.

Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas. Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama. Caso contrário, ela chama os métodos da interface síncrona. Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado. Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.

### <a name="built-in-filter-attributes"></a>Atributos de filtro internos

O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados. Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima. Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.

Atributos de filtro:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Escopos e ordem de execução dos filtros

Um filtro pode ser adicionado ao pipeline com um de três *escopos*:

* Usando um atributo em uma ação.
* Usando um atributo em um controlador.
* Globalmente para todos os controladores e ações, como mostra o código a seguir:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

O código anterior adiciona três filtros globalmente usando a coleção [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).

### <a name="default-order-of-execution"></a>Ordem padrão de execução

Quando há vários filtros *do mesmo tipo*, o escopo determina a ordem padrão de execução do filtro.  Filtros globais envolvem filtros de classe. Filtros de classe envolvem filtros de método.

Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*. A sequência de filtro:

* O código *anterior* dos filtros globais.
  * O código *anterior* dos filtros de controlador.
    * O código *anterior* dos filtros de método de ação.
    * O código *posterior* dos filtros de método de ação.
  * O código *posterior* dos filtros de controlador.
* O código *posterior* dos filtros globais.
  
O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.

| Sequence | Escopo do filtro | Método Filter |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controlador | `OnActionExecuting` |
| 3 | Método | `OnActionExecuting` |
| 4 | Método | `OnActionExecuted` |
| 5 | Controlador | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

Esta sequência mostra:

* O filtro de método está aninhado no filtro de controlador.
* O filtro de controlador está aninhado no filtro global.

### <a name="controller-and-razor-page-level-filters"></a>Filtros no nível do controlador e do Razor Page

Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Estes métodos:

* Encapsulam os filtros executados para uma determinada ação.
* `OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.
* `OnActionExecuted` é chamado após todos os filtros da ação.
* `OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação. O código no filtro após `next` é executado após o método de ação.

Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.

O `TestController`:

* Aplica o `SampleActionFilterAttribute` (`[SampleActionFilter]`) à ação `FilterTest2`.
* Substitui `OnActionExecuting` e `OnActionExecuted`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

Navegar até `https://localhost:5001/Test/FilterTest2` executa o seguinte código:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Para Razor Pages, confira [Implementar filtros do Razor Page substituindo os métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Substituindo a ordem padrão

É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. `IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução. Um filtro com um valor de `Order` menor:

* Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.
* Executa o código *posterior* após um filtro com um valor mais alto de `Order`.

A propriedade `Order` pode ser definida com um parâmetro de construtor:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Considere os mesmos filtros de 3 ações mostrados no exemplo anterior. Se a propriedade `Order` do controlador e os filtros globais estiverem definidos como 1 e 2, respectivamente, a ordem de execução será invertida.

| Sequence | Escopo do filtro | Propriedade `Order` | Método Filter |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Método | 0 | `OnActionExecuting` |
| 2 | Controlador | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Controlador | 1  | `OnActionExecuted` |
| 6 | Método | 0  | `OnActionExecuted` |

A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados. Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar. Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0. Para os filtros internos, o escopo determina a ordem, a menos que você defina `Order` com um valor diferente de zero.

## <a name="cancellation-and-short-circuiting"></a>Cancelamento e curto-circuito

O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro. Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`. O `ShortCircuitingResourceFilter`:

* É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.
* Causa um curto-circuito no restante do pipeline.

Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`. Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro. O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Injeção de dependência

Filtros podem ser adicionados por tipo ou por instância. Se você adicionar uma instância, ela será usada para cada solicitação. Se um tipo for adicionado, ele será ativado pelo tipo. Um filtro ativado por tipo significa:

* Uma instância é criada para cada solicitação.
* Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).

Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)). Dependências de construtor não podem ser fornecidas pela DI porque:

* Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados. 
* Essa é uma limitação do funcionamento dos atributos.

Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.

Os filtros anteriores podem ser aplicados a um método de ação ou controlador:

Os agentes estão disponíveis na DI. No entanto, evite criar e usar filtros apenas para fins de registro em log. O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log. Registro em log adicionado aos filtros:

* Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.
* **Não** deve registrar ações ou outros eventos da estrutura. Os filtros internos registram ações e eventos da estrutura.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`. Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.

O código a seguir mostra `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada. O runtime do ASP.NET Core não garante:

  * Que uma única instância do filtro será criada.
  * Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.

* Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carrega o tipo especificado na DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI. Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:

* Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.  Eles têm suas dependências atendidas pelo contêiner de DI.
* Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.

Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada. O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.

* Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.

O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtros de autorização

Filtros de autorização:

* São os primeiros filtros executados no pipeline de filtro.
* Controlam o acesso aos métodos de ação.
* Têm um método anterior, mas não têm um método posterior.

Filtros de autorização personalizados exigem uma estrutura de autorização personalizada. Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado. O filtro de autorização interno:

* Chama o sistema de autorização.
* Não autoriza solicitações.

**Não** gera exceções dentro de filtros de autorização:

* A exceção não será tratada.
* Os filtros de exceção não tratarão a exceção.

Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.

Saiba mais sobre [Autorização](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtros de recurso

Filtros de recurso:

* Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* A execução encapsula grande parte do pipeline de filtro.
* Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.

Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline. Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.

Exemplos de filtros de recurso:

* [O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Impede o model binding de acessar os dados do formulário.
  * Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.

## <a name="action-filters"></a>Filtros de ação

> [!IMPORTANT]
> Os filtros de ação **não** se aplicam ao Razor Pages. O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>. Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).

Filtros de ação:

* Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* A execução deles envolve a execução de métodos de ação.

O código a seguir mostra um exemplo de filtro de ação:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - permite a leitura das entradas para um método de ação.
* <xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.

Gerar uma exceção em um método de ação:

* Impede a execução de filtros subsequentes.
* Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.

A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção. Definir essa propriedade como nulo:

  * Trata efetivamente a exceção.
  * `Result` é executado como se tivesse retornado do método de ação.

Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Executa todos os próximos filtros de ação e o método de ação.
* Retorna `ActionExecutedContext`.

Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).

A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.

O filtro de ação `OnActionExecuting` pode ser usado para:

* Validar o estado do modelo.
* Retornar um erro se o estado for inválido.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

O método `OnActionExecuted` é executado após o método de ação:

* E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção. Definindo `Exception` como nulo:

  * Trata efetivamente uma exceção.
  * `ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtros de exceção

Filtros de exceção:

* Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>. 
* Podem ser usados para implementar políticas de tratamento de erro comuns.

O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Filtros de exceção:

* Não têm eventos anteriores nem posteriores.
* Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Tratam as exceções sem tratamento que ocorrem no Razor Page ou na criação do controlador, no [model binding](xref:mvc/models/model-binding), nos filtros de ação ou nos métodos de ação.
* **Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.

Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta. Isso interrompe a propagação da exceção. Um filtro de exceção não pode transformar uma exceção em "êxito". Somente um filtro de ação pode fazer isso.

Filtros de exceção:

* São bons para interceptar as exceções que ocorrem nas ações.
* Não são tão flexíveis quanto o middleware de tratamento de erro.

Prefira o middleware para tratamento de exceção. Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado. Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML. Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.

## <a name="result-filters"></a>Filtros de resultado

Filtros de resultado:

* Implemente uma interface:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* A execução deles envolve a execução de resultados de ação.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter e IAsyncResultFilter

O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

O tipo de resultado que está sendo executado depende da ação. Uma ação que retorna um modo de exibição incluiria todo o processamento de Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> em execução. Um método de API pode executar alguma serialização como parte da execução do resultado. Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).

Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação. Os filtros de resultado não são executados quando:

* Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.
* Um filtro de exceção trata uma exceção produzindo um resultado de ação.

O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`. Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia. A geração de uma exceção em `IResultFilter.OnResultExecuting`:

* Impedir a execução do resultado da ação e dos próximos filtros.
* Ser tratada como uma falha e não como um resultado com êxito.

Quando o método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> é executado, a resposta provavelmente já foi enviada para o cliente. Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada ainda mais.

`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.

`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção. Definir `Exception` como nulo trata uma exceção com eficiência e impede que ela seja gerada novamente pelo ASP.NET Core posteriormente no pipeline. Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado. Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.

Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação. Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse. A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter

As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação. Isso inclui resultados de ação produzidos por:

* Filtros de autorização e filtros de recursos que curto-Circuit.
* Filtros de exceção.

Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro. Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`. Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada. Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.

Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

O código anterior pode ser testado executando o [exemplo para download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):

* Invocar as ferramentas de desenvolvedor F12.
* Navegue para `https://localhost:5001/Sample/HeaderWithFactory`.

As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:

* **autor:** `Joe Smith`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **interno:** `My header`

O código anterior cria o cabeçalho de resposta **interno:** `My header`.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implementado em um atributo

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filtros que implementam `IFilterFactory` são úteis para filtros que:

* Não exigem a passagem de parâmetros.
* Tenha dependências de construtor que precisem ser atendidas pela DI.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. `CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Usando middleware no pipeline de filtros

Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline. Mas os filtros diferem do middleware porque fazem parte do ASP.NET Core, o que significa que têm acesso ao contexto e a constructos do ASP.NET Core.

Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros. O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.

## <a name="next-actions"></a>Próximas ações

* Consulte [métodos de filtro para Razor Pages](xref:razor-pages/filter).
* Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
