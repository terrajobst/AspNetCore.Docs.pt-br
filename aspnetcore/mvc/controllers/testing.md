---
title: Lógica do controlador de teste no ASP.NET Core
author: ardalis
description: Saiba como testar a lógica do controlador no ASP.NET Core com o Moq e o xUnit.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2019
uid: mvc/controllers/testing
ms.openlocfilehash: 8dd2fc5d581dbcb11afbcdc0c154c0e2640f9259
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622734"
---
# <a name="test-controller-logic-in-aspnet-core"></a>Lógica do controlador de teste no ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

[Controladores](xref:mvc/controllers/actions) desempenham um papel central em qualquer aplicativo ASP.NET Core MVC. Assim, você precisa estar confiante de que os controladores se comportarão conforme o esperado. Testes automatizados podem detectar erros antes do aplicativo ser implantado em um ambiente de produção.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Testes de unidade da lógica do controlador

Os [testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) envolvem o teste de uma parte de um aplicativo isoladamente em relação à sua infraestrutura e às suas dependências. Quando a unidade está testando a lógica do controlador, somente o conteúdo de uma única ação é testada, não o comportamento de suas dependências ou da estrutura em si.

Configure testes de unidade de ações do controlador para se concentrarem no comportamento do controlador. Um teste de unidade do controlador evita cenários como [filtros](xref:mvc/controllers/filters), [roteamento](xref:fundamentals/routing) ou [model binding](xref:mvc/models/model-binding). Os testes que abrangem as interações entre os componentes que respondem coletivamente a uma solicitação são manipulados pelos *testes de integração*. Para obter mais informações sobre os testes de integração, consulte <xref:test/integration-tests>.

Se você estiver escrevendo filtros e rotas personalizados, realize testes de unidade neles de forma isolada, não como parte dos testes em uma ação do controlador específica.

Para demonstrar testes de unidade do controlador, examine o controlador a seguir no aplicativo de exemplo. O controlador Home exibe uma lista de sessões de debate e permite que novas sessões sejam criadas com uma solicitação POST:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

O controlador anterior:

* Segue o [Princípio de Dependências Explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Espera [DI (injeção de dependência)](xref:fundamentals/dependency-injection) para fornecer uma instância de `IBrainstormSessionRepository`.
* Pode ser testado com um serviço `IBrainstormSessionRepository` fictício usando uma estrutura de objeto fictício, como [Moq](https://www.nuget.org/packages/Moq/). Um *objeto fictício* é um objeto fabricado com um conjunto predeterminado de comportamentos de propriedade e de método usado para teste. Para saber mais, consulte [Introdução aos testes de integração](xref:test/integration-tests#introduction-to-integration-tests).

O método `HTTP GET Index` não tem nenhum loop ou branch e chama apenas um método. O teste de unidade para esta ação:

* Imita o serviço `IBrainstormSessionRepository` usando o método `GetTestSessions`. `GetTestSessions` cria duas sessões de debate fictícias com datas e nomes de sessão.
* Executa o método `Index`.
* Faz declarações sobre o resultado retornado pelo método:
  * Um <xref:Microsoft.AspNetCore.Mvc.ViewResult> é retornado.
  * O [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) é um `StormSessionViewModel`.
  * Há duas sessões de debate armazenadas no `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Os testes método `HTTP POST Index` do controlador Home verificam se:

* Quando [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) é `false`, o método de ação retorna uma *400 Solicitação inválida* <xref:Microsoft.AspNetCore.Mvc.ViewResult> com os dados apropriados.
* Quando `ModelState.IsValid` é `true`:
  * O método `Add` no repositório é chamado.
  * Um <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> é retornado com os argumentos corretos.

O estado de modelo inválido é testado por meio da adição de erros usando <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, conforme mostrado no primeiro teste abaixo:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Quando [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) não for válido, o mesmo `ViewResult` será retornado para uma solicitação GET. O teste não tenta passar um modelo inválido. Passar um modelo inválido não é uma abordagem válida, visto que o model binding não está em execução (embora um [teste de integração](xref:test/integration-tests) use model binding). Nesse caso, o model binding não está sendo testado. Esses testes de unidade estão testando apenas o código no método de ação.

O segundo teste verifica se, quando o `ModelState` é válido:

* Um novo `BrainstormSession` é adicionado (por meio do repositório).
* O método retorna um `RedirectToActionResult` com as propriedades esperadas.

Chamadas fictícias que não são chamadas são normalmente ignoradas, mas a chamada a `Verifiable` no final da chamada de instalação permite a validação fictícia no teste. Isso é realizado com a chamada a `mockRepo.Verify`, que não será aprovada no teste se o método esperado não tiver sido chamado.

> [!NOTE]
> A biblioteca do Moq usada neste exemplo possibilita a combinação de simulações verificáveis ou "estritas" com simulações não verificáveis (também chamadas de simulações "flexíveis" ou stubs). Saiba mais sobre como [personalizar o comportamento de Simulação com o Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) no exemplo de aplicativo exibe informações relacionadas a uma sessão de debate específica. O controlador inclui lógica para lidar com valores `id` inválidos (há dois cenários `return` no exemplo a seguir para abordar esses cenários). A última instrução `return` retorna um novo `StormSessionViewModel` para a exibição (*Controllers/SessionController.cs*):

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Os testes de unidade incluem um teste para cada cenário `return` na ação `Index` do controlador Session:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Mudando para o controlador Ideas, o aplicativo expõe a funcionalidade como uma API Web na rota `api/ideas`:

* Uma lista de ideias (`IdeaDTO`) associada com uma sessão de debate é retornada pelo método `ForSession`.
* O método `Create` adiciona novas ideias a uma sessão.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Evite retornar entidades de domínio de negócios diretamente por meio de chamadas à API. Entidades de domínio:

* Geralmente incluem mais dados do que o cliente necessita.
* Acople desnecessariamente o modelo de domínio interno do aplicativo à API exposta publicamente.

É possível executar o mapeamento entre entidades de domínio e os tipos retornados ao cliente:

* Manualmente com um LINQ `Select`, como o aplicativo de exemplo usa. Para saber mais, consulte [LINQ (Consulta Integrada à Linguagem)](/dotnet/standard/using-linq).
* Automaticamente com uma biblioteca, como [AutoMapper](https://github.com/AutoMapper/AutoMapper).

Em seguida, o aplicativo de exemplo demonstra os testes de unidade para os métodos de API `Create` e `ForSession` do controlador Ideas.

O aplicativo de exemplo contém dois testes `ForSession`. O primeiro teste determina se `ForSession` retorna um <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP não encontrado) para uma sessão inválida:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

O segundo teste `ForSession` determina se `ForSession` retorna uma lista de ideias de sessão (`<List<IdeaDTO>>`) para uma sessão válida. As verificações também examinam a primeira ideia para confirmar se sua propriedade `Name` está correta:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Para testar o comportamento do método `Create` quando o `ModelState` é inválido, o aplicativo de exemplo adiciona um erro de modelo ao controlador como parte do teste. Não tente testar a validação de modelos ou o model binding em testes de unidade&mdash;teste apenas o comportamento do método de ação quando houver um `ModelState` inválido:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

O segundo teste de `Create` depende do repositório retornar `null`, portanto, o repositório fictício é configurado para retornar `null`. Não é necessário criar um banco de dados de teste (na memória ou de outro tipo) e construir uma consulta que retornará esse resultado. O teste pode ser realizado em uma única instrução, como mostrado no código de exemplo:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

O terceiro teste `Create`, `Create_ReturnsNewlyCreatedIdeaForSession`, verifica se o método `UpdateAsync` do repositório é chamado. A simulação é chamada com `Verifiable` e o método `Verify` do repositório fictício é chamado para confirmar se o método verificável é executado. Não é responsabilidade do teste de unidade garantir que o método `UpdateAsync` salve os dados&mdash;isso pode ser realizado com um teste de integração.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultlttgt"></a>Testar ActionResult&lt;T&gt;

No ASP.NET Core 2.1 ou posterior, o [ActionResult&lt;T&gt;](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) permite que você retorne um tipo derivado de `ActionResult` ou retorne um tipo específico.

O aplicativo de exemplo inclui um método que retorna um `List<IdeaDTO>` para uma sessão `id` determinada. Se a sessão `id` não existir, o controlador retornará <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Dois testes do controlador `ForSessionActionResult` estão incluídos no `ApiIdeasControllerTests`.

O primeiro teste confirma se o controlador retorna um `ActionResult`, mas não uma lista de ideias inexistente para uma sessão `id` inexistente:

* O tipo `ActionResult<List<IdeaDTO>>` é `ActionResult`.
* O <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> é um <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Para uma sessão `id` válida, o segundo teste confirma se o método retorna:

* Um `ActionResult` com um tipo `List<IdeaDTO>`.
* O [ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) é um tipo `List<IdeaDTO>`.
* O primeiro item na lista é uma ideia válida que corresponde à ideia armazenada na sessão fictícia (obtida chamando `GetTestSession`).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

O aplicativo de exemplo também inclui um método para criar um novo `Idea` para uma determinada sessão. O controlador retorna:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> para um modelo inválido.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> se a sessão não existir.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> quando a sessão for atualizada com a nova ideia.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Três testes `CreateActionResult` estão incluídos no `ApiIdeasControllerTests`.

O primeiro texto confirma que um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> é retornado para um modelo inválido.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

O segundo teste verifica se um <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> será retornado se a sessão não existir.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Para uma sessão `id` válida, o teste final confirmará se:

* O método retorna um `ActionResult` com um tipo `BrainstormSession`.
* O [ActionResult&lt;T&gt;.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) é um <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult` é semelhante à resposta *201 Criado* com um cabeçalho `Location`.
* O [ActionResult&lt;T&gt;.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) é um tipo `BrainstormSession`.
* A chamada fictícia para atualizar a sessão, `UpdateAsync(testSession)`, foi invocada. A chamada de método `Verifiable` é verificada por meio da execução de `mockRepo.Verify()` nas declarações.
* Dois objetos `Idea` são retornados para a sessão.
* O último item (o `Idea` adicionado pela chamada fictícia a `UpdateAsync`) corresponde ao `newIdea` adicionado à sessão no teste.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/integration-tests>
* [Crie e execute testes de unidade com o Visual Studio](/visualstudio/test/unit-test-your-code).
