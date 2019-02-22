---
title: Tratar solicitações com controladores no ASP.NET Core MVC
author: ardalis
description: ''
ms.author: riande
ms.date: 07/03/2017
uid: mvc/controllers/actions
ms.openlocfilehash: 8289424b3cd3678bea18a25c7850e409795d1577
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410422"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Tratar solicitações com controladores no ASP.NET Core MVC

Por [Steve Smith](https://ardalis.com/) e [Scott Addie](https://github.com/scottaddie)

Controladores, ações e resultados da ação são uma parte fundamental de como os desenvolvedores criam aplicativos usando o ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>O que é um controlador?

Um controlador é usado para definir e agrupar um conjunto de ações. Uma ação (ou *método de ação*) é um método em um controlador que manipula solicitações. Os controladores agrupam ações semelhantes de forma lógica. Essa agregação de ações permite que conjuntos de regras comuns, como roteamento, cache e autorização, sejam aplicados em conjunto. As solicitações são mapeadas para ações por meio de [roteamento](xref:mvc/controllers/routing).

Por convenção, as classes do controlador:
* Residem na pasta *Controllers* no nível raiz do projeto
* Herdam de `Microsoft.AspNetCore.Mvc.Controller`

Um controlador é uma classe que pode ser instanciada, em que, pelo menos, uma das seguintes condições é verdadeira:
* O nome da classe tem "Controller" como sufixo
* A classe herda de uma classe cujo nome tem "Controller" como sufixo
* A classe está decorada com o atributo `[Controller]`

Uma classe de controlador não deve ter um atributo `[NonController]` associado.

Os controladores devem seguir o [Princípio de Dependências Explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies). Há duas abordagens para implementar esse princípio. Se várias ações do controlador exigem o mesmo serviço, considere o uso da [injeção de construtor](xref:mvc/controllers/dependency-injection#constructor-injection) para solicitar essas dependências. Se o serviço é necessário para um único método de ação, considere o uso da [Injeção de Ação](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) para solicitar a dependência.

Dentro do padrão **M**odel-**V**iew-**C**ontroller, um controlador é responsável pelo processamento inicial da solicitação e criação de uma instância do modelo. Em geral, as decisões de negócios devem ser tomadas dentro do modelo.

O controlador usa o resultado do processamento do modelo (se houver) e retorna a exibição correta e seus dados da exibição associada ou o resultado da chamada à API. Saiba mais em [Visão geral do ASP.NET Core MVC](xref:mvc/overview) e em [Introdução ao ASP.NET Core MVC e ao Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

O controlador é uma abstração no *nível da interface do usuário*. Suas responsabilidades são garantir que os dados de solicitação sejam válidos e escolher qual exibição (ou resultado de uma API) deve ser retornada. Em aplicativos bem fatorados, ele não inclui diretamente o acesso a dados ou a lógica de negócios. Em vez disso, o controlador delega essas responsabilidades a serviços.

## <a name="defining-actions"></a>Definindo ações

Métodos públicos em um controlador, exceto aqueles decorados com o atributo `[NonAction]`, são ações. Parâmetros em ações são associados aos dados de solicitação e validados usando o [model binding](xref:mvc/models/model-binding). A validação de modelo ocorre em tudo o que é associado ao modelo. O valor da propriedade `ModelState.IsValid` indica se o model binding e a validação foram bem-sucedidas.

Métodos de ação devem conter uma lógica para mapear uma solicitação para um interesse de negócios. Normalmente, interesses de negócios devem ser representados como serviços acessados pelo controlador por meio da [injeção de dependência](xref:mvc/controllers/dependency-injection). Em seguida, as ações mapeiam o resultado da ação de negócios para um estado do aplicativo.

As ações podem retornar qualquer coisa, mas frequentemente retornam uma instância de `IActionResult` (ou `Task<IActionResult>` para métodos assíncronos) que produz uma resposta. O método de ação é responsável por escolher *o tipo de resposta*. O resultado da ação *é responsável pela resposta*.

### <a name="controller-helper-methods"></a>Métodos auxiliares do controlador

Os controladores geralmente herdam de [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), embora isso não seja necessário. A derivação de `Controller` fornece acesso a três categorias de métodos auxiliares:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. Métodos que resultam em um corpo de resposta vazio

Nenhum cabeçalho de resposta HTTP `Content-Type` é incluído, pois o corpo da resposta não tem nenhum conteúdo a ser descrito.

Há dois tipos de resultados nessa categoria: Redirecionar e Código de Status HTTP.

* **Código de Status HTTP**

    Esse tipo retorna um código de status HTTP. Alguns métodos auxiliares desse tipo são `BadRequest`, `NotFound` e `Ok`. Por exemplo, `return BadRequest();` produz um código de status 400 quando executado. Quando métodos como `BadRequest`, `NotFound` e `Ok` estão sobrecarregados, eles deixam de se qualificar como respondentes do Código de Status HTTP, pois a negociação de conteúdo está em andamento.

* **Redirecionamento**

    Esse tipo retorna um redirecionamento para uma ação ou um destino (usando `Redirect`, `LocalRedirect`, `RedirectToAction` ou `RedirectToRoute`). Por exemplo, `return RedirectToAction("Complete", new {id = 123});` redireciona para `Complete`, passando um objeto anônimo.

    O tipo de resultado do Redirecionamento é diferente do tipo do Código de Status HTTP, principalmente na adição de um cabeçalho de resposta HTTP `Location`.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. Métodos que resultam em um corpo de resposta não vazio com um tipo de conteúdo predefinido

A maioria dos métodos auxiliares desta categoria inclui uma propriedade `ContentType`, permitindo que você defina o cabeçalho de resposta `Content-Type` para descrever o corpo da resposta.

Há dois tipos de resultados nessa categoria: [Exibições](xref:mvc/views/overview) e [Resposta Formatada](xref:web-api/advanced/formatting).

* **Exibir**

    Esse tipo retorna uma exibição que usa um modelo para renderizar HTML. Por exemplo, `return View(customer);` passa um modelo para a exibição para associação de dados.

* **Resposta Formatada**

    Esse tipo retorna JSON ou um formato de troca de dados semelhante para representar um objeto de uma maneira específica. Por exemplo, `return Json(customer);` serializa o objeto fornecido no formato JSON.
    
    Outros métodos comuns desse tipo incluem `File` e `PhysicalFile`. Por exemplo, `return PhysicalFile(customerFilePath, "text/xml");` retorna [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. Métodos que resultam em um corpo de resposta não vazio formatado em um tipo de conteúdo negociado com o cliente

Essa categoria é mais conhecida como **Negociação de Conteúdo**. A [Negociação de conteúdo](xref:web-api/advanced/formatting#content-negotiation) aplica-se sempre que uma ação retorna um tipo [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) ou algo diferente de uma implementação [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult). Uma ação que retorna uma implementação não `IActionResult` (por exemplo, `object`) também retorna uma Resposta Formatada.

Alguns métodos auxiliares desse tipo incluem `BadRequest`, `CreatedAtRoute` e `Ok`. Exemplos desses métodos incluem `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);` e `return Ok(value);`, respectivamente. Observe que `BadRequest` e `Ok` fazem a negociação de conteúdo apenas quando um valor é passado; sem que um valor seja passado, eles atuam como tipos de resultado do Código de Status HTTP. Por outro lado, o método `CreatedAtRoute` sempre faz a negociação de conteúdo, pois todas as suas sobrecargas exigem que um valor seja passado.

### <a name="cross-cutting-concerns"></a>Interesses paralelos

Normalmente, os aplicativos compartilham partes de seu fluxo de trabalho. Exemplos incluem um aplicativo que exige autenticação para acessar o carrinho de compras ou um aplicativo que armazena dados em cache em algumas páginas. Para executar a lógica antes ou depois de um método de ação, use um *filtro*. A utilização de [filtros](xref:mvc/controllers/filters) em interesses paralelos pode reduzir a duplicação.

A maioria dos atributos de filtro, como `[Authorize]`, pode ser aplicada no nível do controlador ou da ação, dependendo do nível desejado de granularidade.

O tratamento de erro e o cache de resposta costumam ser interesses paralelos:
   * [Tratar erros](xref:mvc/controllers/filters#exception-filters)
   * [Cache de resposta](xref:performance/caching/response)

Muitos interesses paralelos podem ser abordados com filtros ou um [middleware](xref:fundamentals/middleware/index) personalizado.
