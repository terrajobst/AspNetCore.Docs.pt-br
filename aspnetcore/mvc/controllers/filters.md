---
title: Filtros no ASP.NET Core
author: ardalis
description: Saiba como os filtros funcionam e como usá-los no ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2019
uid: mvc/controllers/filters
ms.openlocfilehash: 4fe04cde2a234302845b2cbded106f1e809842bc
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209288"
---
# <a name="filters-in-aspnet-core"></a>Filtros no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)

Os *Filtros* no ASP.NET Core MVC permitem executar código antes ou depois de determinados estágios do pipeline de processamento de solicitações.

O filtros internos lidam com tarefas como:

* Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).
* Garantir que todas as solicitações usem HTTPS.
* Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache). 

É possível criar filtros personalizados para lidar com interesses paralelos. Os filtros podem evitar a duplicação de código entre as ações. Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.

[Exibir ou baixar amostra do GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

## <a name="how-filters-work"></a>Como os filtros funcionam

Os filtros são executados dentro do *pipeline de invocação de ações do MVC*, às vezes chamado de *pipeline de filtros*.  O pipeline de filtros é executado após o MVC selecionar a ação a ser executada.

![A solicitação é processada por meio de Outro Middleware, do Middleware de Roteamento, da Seleção de Ação e do Pipeline de Invocação de Ações do MVC. O processamento de solicitações continua por meio da Seleção de Ação, do Middleware de Roteamento e de diversos Outros Middlewares antes de se tornar uma resposta enviada ao cliente.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Tipos de filtro

Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros.

* [Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário atual tem autorização para a solicitação atual. Eles podem fazer um curto-circuito do pipeline quando uma solicitação não é autorizada. 

* [Filtros de recurso](#resource-filters) são os primeiros a lidar com uma solicitação após a autorização.  Eles podem executar código antes do restante do pipeline de filtros e após o restante do pipeline ser concluído. Esses filtros são úteis para implementar o cache ou para, de alguma forma, fazer o curto-circuito do pipeline de filtros por motivos de desempenho. Eles são executados antes do model binding, portanto, podem influenciar o model binding.

* [Filtros de ação](#action-filters) podem executar código imediatamente antes e depois de um método de ação individual ser chamado. Eles podem ser usados para manipular os argumentos passados para uma ação, bem como o resultado da ação. Os filtros de ação não são compatíveis com o Razor Pages.

* [Filtros de exceção](#exception-filters) são usados para aplicar políticas globais para exceções sem tratamento que ocorrem antes que qualquer coisa tenha sido gravada no corpo da resposta.

* [Filtros de resposta](#result-filters) podem executar código imediatamente antes e depois da execução de resultados de ações individuais. Eles são executados somente quando o método de ação é executado com êxito. Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.

O diagrama a seguir mostra como esses tipos de filtro interagem no pipeline de filtros.

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado. Na saída, a solicitação é processada somente por Filtros de resultado e Filtros de recurso antes de se tornar uma resposta enviada ao cliente.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementação

Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface. 

Filtros síncronos que podem executar código antes e depois do estágio do pipeline definem os métodos On*Stage*Executing e On*Stage*Executed. Por exemplo, `OnActionExecuting` é chamado antes que o método de ação seja chamado e `OnActionExecuted` é chamado após o método de ação retornar.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet1)]

Filtros assíncronos definem um único método On*Stage*ExecutionAsync. Esse método usa um delegado *FilterType*ExecutionDelegate, que executa o estágio de pipeline do filtro. Por exemplo, `ActionExecutionDelegate` chama o método de ação ou o próximo filtro de ação, e você pode executar código antes e depois de chamá-lo.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

É possível implementar interfaces para vários estágios do filtro em uma única classe. Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e os respectivos equivalentes assíncronos.

> [!NOTE]
> Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, não ambas. Primeiro, a estrutura verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama. Caso contrário, ela chama os métodos da interface síncrona. Se você implementasse as duas interfaces em uma classe, somente o método assíncrono seria chamado. Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, você substituiria apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.

### <a name="ifilterfactory"></a>IFilterFactory

[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro. Quando se prepara para invocar o filtro, a estrutura tenta convertê-lo em um `IFilterFactory`. Se essa conversão for bem-sucedida, o método [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) será chamado para criar a instância `IFilterMetadata` que será invocada. Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.

Você pode implementar `IFilterFactory` em suas próprias implementações de atributo como outra abordagem à criação de filtros:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a>Atributos de filtro internos

A estrutura inclui filtros internos baseados em atributos que você pode organizar em subclasses e personalizar. Por exemplo, o seguinte Filtro de resultado adiciona um cabeçalho à resposta.

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima. Você adicionaria esse atributo a um método de ação ou controlador e especificaria o nome e o valor do cabeçalho HTTP:

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

O resultado da ação `Index` é mostrado abaixo – os cabeçalhos de resposta são exibidos na parte inferior direita.

![Ferramentas para Desenvolvedores do Microsoft Edge mostrando cabeçalhos de resposta, inclusive o autor Steve Smith @ardalis](filters/_static/add-header.png)

Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.

Atributos de filtro:

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

`TypeFilterAttribute` e `ServiceFilterAttribute` são explicados [posteriormente neste artigo](#dependency-injection).

## <a name="filter-scopes-and-order-of-execution"></a>Escopos e ordem de execução dos filtros

Um filtro pode ser adicionado ao pipeline com um de três *escopos*. É possível adicionar um filtro a um método de ação específico ou a uma classe de controlador usando um atributo. Ou você pode registrar um filtro globalmente para todos os controladores e ações. Os filtros são adicionados globalmente quando são adicionados à coleção `MvcOptions.Filters` em `ConfigureServices`:

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a>Ordem padrão de execução

Quando há vários filtros para um determinado estágio do pipeline, o escopo determina a ordem padrão de execução dos filtros.  Filtros globais circundam filtros de classe, que, por sua vez, circundam filtros de método. Isso é, às vezes, chamado de aninhamento de "bonecas russas", pois cada aumento de escopo é encapsulado em torno do escopo anterior, como uma [matriosca](https://wikipedia.org/wiki/Matryoshka_doll). Normalmente, você obtém o comportamento de substituição desejado sem precisar determinar uma ordem explicitamente.

Como resultado desse aninhamento, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*. A sequência é semelhante a esta:

* O código *anterior* de filtros aplicados globalmente
  * O código *anterior* de filtros aplicados a controladores
    * O código *anterior* de filtros aplicados a métodos de ação
    * O código *posterior* de filtros aplicados a métodos de ação
  * O código *posterior* de filtros aplicados a controladores
* O código *posterior* de filtros aplicados globalmente
  
Este é um exemplo que ilustra a ordem na qual métodos de filtro são chamados para filtros de ação síncronos.

| Sequência | Escopo do filtro | Método do filtro |
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

Para colocar de outra forma, se você estiver dentro do método On*Stage*ExecutionAsync de um filtro assíncrono, todos os filtros com escopo mais estreito serão executados enquanto seu código estiver na pilha.

> [!NOTE]
> Cada controlador que herda da classe base `Controller` inclui os métodos `OnActionExecuting` e `OnActionExecuted`. Esses métodos encapsulam os filtros que são executados para uma determinada ação: `OnActionExecuting` é chamado antes de qualquer um dos filtros e `OnActionExecuted` é chamado após todos os filtros.

### <a name="overriding-the-default-order"></a>Substituindo a ordem padrão

É possível substituir a sequência padrão de execução implementando `IOrderedFilter`. Essa interface expõe uma propriedade `Order` que tem precedência sobre o escopo para determinar a ordem de execução. Um filtro com um valor mais baixo de `Order` terá seu código *anterior* executado antes que um filtro com um valor mais alto de `Order`. Um filtro com um valor mais baixo de `Order` terá seu código *posterior* executado depois que um filtro com um valor mais alto de `Order`. Você pode definir a propriedade `Order` usando um parâmetro de construtor:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Se você tiver os mesmos três filtros de ação mostrados no exemplo anterior, mas definir a propriedade `Order` dos filtros de controlador e global como 1 e 2 respectivamente, a ordem de execução será invertida.

| Sequência | Escopo do filtro | Propriedade `Order` | Método do filtro |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Método | 0 | `OnActionExecuting` |
| 2 | Controlador | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Controlador | 1  | `OnActionExecuted` |
| 6 | Método | 0  | `OnActionExecuted` |

A propriedade `Order` tem precedência sobre o escopo ao determinar a ordem na qual os filtros serão executados. Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar. Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0. Para os filtros internos, o escopo determina a ordem, a menos que você defina `Order` com um valor diferente de zero.

## <a name="cancellation-and-short-circuiting"></a>Cancelamento e curto-circuito

Você pode fazer um curto-circuito no pipeline de filtros a qualquer momento, definindo a propriedade `Result` no parâmetro `context` fornecido ao método do filtro. Por exemplo, o filtro de recurso a seguir impede que o resto do pipeline seja executado.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`. O `ShortCircuitingResourceFilter`:

* É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.
* Causa um curto-circuito no restante do pipeline.

Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`. Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro. O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Injeção de dependência

Filtros podem ser adicionados por tipo ou por instância. Se você adicionar uma instância, ela será usada para cada solicitação. Se você adicionar um tipo, ele será ativado pelo tipo, o que significa que uma instância será criada para cada solicitação e as dependências de construtor serão populadas pela DI ([injeção de dependência](../../fundamentals/dependency-injection.md)). Adicionar um filtro por tipo é equivalente a `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.

Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](../../fundamentals/dependency-injection.md)). Isso ocorre porque os atributos precisam ter os parâmetros de construtor fornecidos quando eles são aplicados. Essa é uma limitação do funcionamento dos atributos.

Se seus filtros tiverem dependências que você precisa acessar da DI, há várias abordagens com suporte. É possível aplicar o filtro a um método de ação ou classe usando uma das opções a seguir:

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* `IFilterFactory` implementado em seu atributo

> [!NOTE]
> Uma dependência que talvez você queira obter da DI é um agente. No entanto, evite criar e usar filtros apenas para fins de registro em log, pois é possível que os [recursos de registro em log da estrutura interna](xref:fundamentals/logging/index) já forneçam o que você precisa. Se você for adicionar o registro em log a seus filtros, ele deve se concentrar em questões referentes ao domínio de negócios ou ao comportamento específico de seu filtro, em vez de ações do MVC ou outros eventos da estrutura.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Tipos de implementação do filtro de serviço são registrados em DI. Um `ServiceFilterAttribute` recupera uma instância do filtro da DI. Adicione o `ServiceFilterAttribute` ao contêiner em `Startup.ConfigureServices` e faça uma referência a ele em um atributo `[ServiceFilter]`:

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Ao usar `ServiceFilterAttribute`, configurar `IsReusable` é uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que ele foi criada. A estrutura não fornece garantias de que uma única instância do filtro vá ser criada ou que o filtro não será solicitado novamente do contêiner de DI em algum momento posterior. Evite usar `IsReusable` ao usar um filtro que dependa dos serviços com um tempo de vida diferente de singleton.

Usar `ServiceFilterAttribute` sem registrar o tipo de filtro gera uma exceção:

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

`ServiceFilterAttribute` implementa `IFilterFactory`. `IFilterFactory` expõe o método `CreateInstance` para criar uma instância de `IFilterMetadata`. O método `CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).

### <a name="typefilterattribute"></a>TypeFilterAttribute

O `TypeFilterAttribute` é semelhante ao `ServiceFilterAttribute`, mas seu tipo não é resolvido diretamente por meio do contêiner de DI. Ele cria uma instância do tipo usando `Microsoft.Extensions.DependencyInjection.ObjectFactory`.

Por causa dessa diferença:

* Os tipos que são referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner primeiro.  As dependências deles são atendidas pelo contêiner. 
* Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.

Ao usar `TypeFilterAttribute`, configurar `IsReusable` é uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que ele foi criada. A estrutura não fornece nenhuma garantia de que uma única instância do filtro será criada. Evite usar `IsReusable` ao usar um filtro que dependa dos serviços com um tempo de vida diferente de singleton.

O exemplo a seguir demonstra como passar argumentos para um tipo usando `TypeFilterAttribute`:

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

### <a name="ifilterfactory-implemented-on-your-attribute"></a>IFilterFactory implementado em seu atributo

Se você tiver um filtro que:

* Não exija nenhum argumento.
* Tenha dependências de construtor que precisem ser atendidas pela DI.

Você pode usar seu próprio atributo nomeado em classes e métodos em vez de `[TypeFilter(typeof(FilterType))]`). O filtro a seguir mostra como isso pode ser implementado:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Esse filtro pode ser aplicado a classes ou métodos usando a sintaxe `[SampleActionFilter]`, em vez de precisar usar `[TypeFilter]` ou `[ServiceFilter]`.

## <a name="authorization-filters"></a>Filtros de autorização

*Filtros de autorização*:

* Controlam o acesso aos métodos de ação.
* São os primeiros filtros a serem executados no pipeline de filtro. 
* Têm um método anterior, mas não têm um método posterior. 

Você deve escrever somente um filtro de autorização personalizado se estiver escrevendo sua própria estrutura de autorização. Prefira configurar suas políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado. A implementação do filtro interno é responsável somente por chamar o sistema de autorização.

Você não deve gerar exceções dentro de filtros de autorização, pois nada tratará a exceção (os filtros de exceção não as tratarão). Considere a possibilidade de emitir um desafio quando ocorrer uma exceção.

Saiba mais sobre [Autorização](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtros de recurso

* Implementam a interface `IResourceFilter` ou `IAsyncResourceFilter`.
* Suas execuções encapsulam a maior parte do pipeline de filtro. 
* Somente os [Filtros de autorização](#authorization-filters) são executados antes dos Filtros de recurso.

Os filtros de recursos são úteis para causar um curto-circuito na maior parte do trabalho que uma solicitação faz. Por exemplo, um filtro de cache poderá evitar o restante do pipeline se a resposta estiver no cache.

O [filtro de recurso com curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente é um exemplo de filtro de recurso. Outro exemplo é [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

* Essa opção impedirá o model binding de acessar os dados do formulário. 
* Ela é útil para uploads de arquivos grandes e para impedir que o formulário seja lido na memória.

## <a name="action-filters"></a>Filtros de ação

> [!IMPORTANT]
> Os filtros de ação **não** se aplicam ao Razor Pages. O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>. Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).

*Filtros de ação*:

* Implementam a interface `IActionFilter` ou `IAsyncActionFilter`.
* A execução deles envolve a execução de métodos de ação.

Veja um exemplo de filtro de ação:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:

* `ActionArguments` – permite manipular as entradas para a ação.
* `Controller` – permite manipular a instância do controlador. 
* `Result` – defini-lo faz um curto-circuito da execução do método de ação e dos filtros de ação posteriores. Apresentar uma exceção também impete a execução do método de ação e dos filtros posteriores, mas isso é tratado como uma falha, e não como um resultado bem-sucedido.

A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:

* `Canceled` – será verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.
* `Exception` – não será nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção. Definir essa propriedade como nula “manipula” uma exceção efetivamente e `Result` será executado como se tivesse sido retornado do método de ação normalmente.

Para um `IAsyncActionFilter`, uma chamada para o `ActionExecutionDelegate`:

* Executa todos os próximos filtros de ação e o método de ação.
* Retorna `ActionExecutedContext`. 

Para fazer um curto-circuito, atribua `ActionExecutingContext.Result` a uma instância de resultado e não chame o `ActionExecutionDelegate`.

A estrutura fornece um `ActionFilterAttribute` abstrato que você pode colocar em uma subclasse. 

Você poderá usar um filtro de ação para validar o estado do modelo e retornar erros se o estado for inválido:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

O método `OnActionExecuted` é executado depois do método de ação e pode ver e manipular os resultados da ação por meio da propriedade `ActionExecutedContext.Result`. `ActionExecutedContext.Canceled` será definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro. `ActionExecutedContext.Exception` será definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção. Definindo `ActionExecutedContext.Exception` como nulo:

* 'Trata' efetivamente uma exceção.
* `ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.

## <a name="exception-filters"></a>Filtros de exceção

*Filtros de exceção* implementam a interface `IExceptionFilter` ou `IAsyncExceptionFilter`. Eles podem ser usados para implementar políticas de tratamento de erro comuns para um aplicativo. 

O exemplo de filtro de exceção a seguir usa uma exibição de erro do desenvolvedor personalizada para exibir detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

Filtros de exceção:

* Não têm eventos anteriores nem posteriores. 
* Implementam `OnException` ou `OnExceptionAsync`. 
* Tratam as exceções sem tratamento que ocorrem na criação do controlador, no [model binding](../models/model-binding.md), nos filtros de ação ou nos métodos de ação. 
* Não capturam as exceções que ocorrem em Filtros de recurso, em Filtros de resultado ou na execução do Resultado de MVC.

Para tratar uma exceção, defina a propriedade `ExceptionContext.ExceptionHandled` como verdadeiro ou grave uma resposta. Isso interrompe a propagação da exceção. Um Filtro de exceção não pode transformar uma exceção em "êxito". Somente um filtro de ação pode fazer isso.

> [!NOTE]
> No ASP.NET Core 1.1, a resposta não será enviada se você definir `ExceptionHandled` como true **e** gravar uma resposta. Nesse cenário, o ASP.NET Core 1.0 envia a resposta e o ASP.NET Core 1.1.2 retorna ao comportamento do 1.0. Para obter mais informações, consulte [problema #5594](https://github.com/aspnet/Mvc/issues/5594) no repositório do GitHub. 

Filtros de exceção:

* São bons para interceptar as exceções que ocorrem nas ações de MVC.
* Não são tão flexíveis quanto o middleware de tratamento de erro. 

Prefira o middleware para tratamento de exceção. Use filtros de exceção somente quando você precisar fazer o tratamento de erro *de forma diferente* com base na ação de MVC escolhida. Por exemplo, seu aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML. Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.

O `ExceptionFilterAttribute` pode tornar-se uma subclasse. 

## <a name="result-filters"></a>Filtros de resultado

* Implemente uma interface:
  * `IResultFilter` ou `IAsyncResultFilter`.
  * `IAlwaysRunResultFilter` ou `IAsyncAlwaysRunResultFilter`
* A execução deles envolve a execução de resultados de ação. 

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter e IAsyncResultFilter

Veja um exemplo de um filtro de resultado que adiciona um cabeçalho HTTP.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

O tipo de resultado que está sendo executado depende da ação em questão. Uma ação de MVC que retorna um modo de exibição incluiria todo o processamento de Razor como parte do `ViewResult` em execução. Um método de API pode executar alguma serialização como parte da execução do resultado. Saiba mais sobre [resultados de ação](actions.md)

Filtros de resultado são executados somente para resultados bem-sucedidos – quando a ação ou filtros da ação produzem um resultado de ação. Filtros de resultado não são executados quando filtros de exceção tratam uma exceção.

O método `OnResultExecuting` pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo `ResultExecutingContext.Cancel` como verdadeiro. De modo geral, você deve gravar no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia. A geração de uma exceção vai:

* Impedir a execução do resultado da ação e dos próximos filtros.
* Ser tratada como uma falha e não como um resultado com êxito.

Quando o método `OnResultExecuted` é executado, a resposta provavelmente foi enviada ao cliente e não pode mais ser alterada (a menos que uma exceção tenha sido apresentada). `ResultExecutedContext.Canceled` será definido como verdadeiro se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.

`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver apresentado uma exceção. Definir `Exception` para como nulo “trata” uma exceção com eficiência e impede que a exceção seja apresentada novamente pelo MVC posteriormente no pipeline. Quando está tratando uma exceção em um filtro de resultado, talvez você não possa gravar dados na resposta. Se o resultado da ação for apresentado durante sua execução e os cabeçalhos já tiverem sido liberados para o cliente, não haverá nenhum mecanismo confiável para enviar um código de falha.

Para um `IAsyncResultFilter`, uma chamada para `await next` no `ResultExecutionDelegate` executa qualquer filtro de resultado posterior e o resultado da ação. Para fazer um curto-circuito, defina `ResultExecutingContext.Cancel` para verdadeiro e não chame `ResultExecutionDelegate`.

A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse. A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter

As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para resultados de ação. O filtro é aplicado ao resultado de uma ação, a menos que <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> se aplique e cause curto-circuito da resposta.

Em outras palavras, esses filtros "sempre executar" estão sempre em execução, exceto quando um filtro de exceção ou autorização causar curto-circuito neles. Filtra o que não é `IExceptionFilter`, e `IAuthorizationFilter` não causa curto-circuito.

Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:

```csharp
public class UnprocessableResultFilter : Attribute, IAlwaysRunResultFilter
{
    public void OnResultExecuting(ResultExecutingContext context)
    {
        if (context.Result is StatusCodeResult statusCodeResult &&
            statusCodeResult.StatusCode == 415)
        {
            context.Result = new ObjectResult("Can't process this!")
            {
                StatusCode = 422,
            };
        }
    }

    public void OnResultExecuted(ResultExecutedContext context)
    {
    }
}
```

## <a name="using-middleware-in-the-filter-pipeline"></a>Usando middleware no pipeline de filtros

Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline. Mas os filtros diferem do middleware porque fazem parte do MVC, o que significa que eles têm acesso ao contexto e a constructos do MVC.

No ASP.NET Core 1.1, você pode usar o middleware no pipeline de filtros. Talvez você queira fazer isso se tiver um componente de middleware que precisa acessar dados de rota do MVC ou que precisa ser executado somente para determinados controladores ou ações.

Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware que você deseja injetar no pipeline de filtros. Veja um exemplo que usa o middleware de localização para estabelecer a cultura atual para uma solicitação:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

Depois, você pode usar o `MiddlewareFilterAttribute` para executar o middleware para um controlador ou ação selecionada ou para executá-lo globalmente:

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.

## <a name="next-actions"></a>Próximas ações

* Confira [Métodos de filtro do Razor Pages](xref:razor-pages/filter)
* Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do Github](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).
