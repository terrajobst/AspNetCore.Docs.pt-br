---
title: Testes de unidade de páginas do Razor no ASP.NET Core
author: guardrex
description: Saiba como criar testes de unidade para aplicativos de páginas do Razor.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f89b4fcb0065e725f70deec7859e373f9158b4bd
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622787"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Testes de unidade de páginas do Razor no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

ASP.NET Core dá suporte a testes de unidade de aplicativos de páginas do Razor. Testes dos dados (DAL) da camada de acesso e modelos de página ajudam a garantir:

* Partes de um aplicativo páginas Razor trabalhem independentemente ou em conjunto como uma unidade durante a construção do aplicativo.
* Classes e métodos limitaram escopos de responsabilidade.
* Existe documentação adicional sobre como o aplicativo deve se comportar.
* As regressões, que são trazidos por atualizações para o código de erros, são encontradas durante a implantação e compilação automatizada.

Este tópico pressupõe que você tenha uma compreensão básica dos aplicativos das páginas do Razor e testes de unidade. Se você estiver familiarizado com conceitos de teste ou de aplicativos de páginas do Razor, consulte os tópicos a seguir:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Teste de unidade em C# no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O projeto de exemplo é composto de dois aplicativos:

| Aplicativo         | Pasta do projeto                     | Descrição |
| ----------- | ---------------------------------- | ----------- |
| Aplicativo de mensagens | *src/RazorPagesTestSample*         | Permite que um usuário adicionar uma mensagem, excluir uma mensagem, excluir todas as mensagens e analisar mensagens (localizar o número médio de palavras por mensagem). |
| Aplicativo de teste    | *tests/RazorPagesTestSample.Tests* | Usado para teste de unidade da DAL e modelo de página de índice do aplicativo de mensagem. |

Os testes podem ser executados usando os recursos de teste interno de um IDE, como [Visual Studio](/visualstudio/test/unit-test-your-code) ou [Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Se usando [Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando na *tests/RazorPagesTestSample.Tests* pasta:

```console
dotnet test
```

## <a name="message-app-organization"></a>Organização de aplicativo de mensagem

O aplicativo de mensagem é um sistema de mensagem de páginas do Razor com as seguintes características:

* A página de índice do aplicativo (*Pages* e *Pages/Index.cshtml.cs*) fornece uma interface do usuário e a página de métodos de modelo para controlar a adição, exclusão e análise de mensagens (localizar o número médio de palavras por mensagem).
* Uma mensagem é descrita pela `Message` classe (*Data/Message.cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). O `Text` propriedade é necessária e é limitada a 200 caracteres.
* As mensagens são armazenadas usando [banco de dados do Entity Framework na memória](/ef/core/providers/in-memory/)&#8224;.
* O aplicativo contém uma DAL na sua classe de contexto do banco de dados, `AppDbContext` (*Data/AppDbContext.cs*). Os métodos DAL são marcados como `virtual`, que permite que os métodos para uso em testes de simulação.
* Se o banco de dados está vazio na inicialização do aplicativo, o repositório de mensagens é inicializado com três mensagens. Eles *propagado mensagens* também são usados em testes.

&#8224;O tópico EF [testar com InMemory](/ef/core/miscellaneous/testing/in-memory), explica como usar um banco de dados na memória para testes com MSTest. Este tópico usa o [xUnit](https://xunit.github.io/) estrutura de teste. Conceitos de teste e teste implementações em estruturas de teste diferentes são semelhantes, mas não idênticos.

Embora o aplicativo de exemplo não usa o padrão de repositório e não é um exemplo efetivação do [padrão de unidade de trabalho (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), páginas do Razor dá suporte a esses padrões de desenvolvimento. Para obter mais informações, consulte [Projetando a camada de persistência de infraestrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e <xref:mvc/controllers/testing> (o exemplo implementa o padrão de repositório).

## <a name="test-app-organization"></a>Organização de aplicativo de teste

O aplicativo de teste é um aplicativo de console dentro de *tests/RazorPagesTestSample.Tests* pasta.

| Pasta do aplicativo de teste | Descrição |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contém os testes de unidade para o DAL.</li><li>*IndexPageTests.cs* contém os testes de unidade para o modelo de página de índice.</li></ul> |
| *Utilitários*     | Contém o `TestDbContextOptions` método usado para criar o novo banco de dados opções de contexto para cada teste de unidade da DAL, de modo que o banco de dados é redefinido como sua condição de linha de base para cada teste. |

É a estrutura de teste [xUnit](https://xunit.github.io/). É o objeto de objetos fictícios [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testes de unidade de dados (DAL) da camada de acesso

O aplicativo de mensagem tem uma DAL com quatro métodos contidos na `AppDbContext` classe (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Cada método tem um ou dois testes de unidade no aplicativo de teste.

| Método DAL               | Função                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtém uma `List<Message>` do banco de dados classificado pelo `Text` propriedade. |
| `AddMessageAsync`        | Adiciona um `Message` no banco de dados.                                          |
| `DeleteAllMessagesAsync` | Exclui todos os `Message` entradas do banco de dados.                           |
| `DeleteMessageAsync`     | Exclui um único `Message` do banco de dados por `Id`.                      |

Testes de unidade da DAL requerem <xref:Microsoft.EntityFrameworkCore.DbContextOptions> ao criar um novo `AppDbContext` para cada teste. Uma abordagem para criar o `DbContextOptions` para cada teste é usar um <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

O problema com essa abordagem é que cada teste recebe o banco de dados no estado em que o teste anterior a deixou. Isso pode ser um problema ao tentar escrever testes de unidade atômica que não interfiram uns aos outros. Para forçar o `AppDbContext` para usar um novo contexto de banco de dados para cada teste, forneça um `DbContextOptions` instância com base em um novo provedor de serviço. O aplicativo de teste mostra como fazer isso usando seus `Utilities` método de classe `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Usando o `DbContextOptions` na unidade de DAL testes permite que cada teste executado atomicamente com uma instância de banco de dados atualizado:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Cada método de teste na `DataAccessLayerTest` classe (*UnitTests/DataAccessLayerTest.cs*) segue um padrão semelhante Arrange, Act-Assert:

1. Organize: O banco de dados está configurado para o teste de e/ou o resultado esperado é definido.
1. ACT: O teste é executado.
1. Assert: Asserções são feitas para determinar se o resultado do teste é um sucesso.

Por exemplo, o `DeleteMessageAsync` método é responsável por remover uma única mensagem identificada pelo seu `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Há dois testes para esse método. Um teste verifica que o método exclui uma mensagem quando a mensagem estiver presente no banco de dados. Os outros testes de método que o banco de dados não serão alteradas se a mensagem `Id` para exclusão não existe. O `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` método é mostrado abaixo:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Primeiro, o método executa a etapa Arranjar, onde ocorre a preparação para a etapa atuar. As mensagens de propagação são obtidas e mantidas em `seedMessages`. As mensagens de propagação são salvos no banco de dados. A mensagem com um `Id` de `1` é definido para exclusão. Quando o `DeleteMessageAsync` método é executado, as mensagens esperadas devem ter todas as mensagens, exceto aquele com um `Id` de `1`. O `expectedMessages` variável representa esse resultado esperado.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

O método funciona: O `DeleteMessageAsync` método é executado passando a `recId` de `1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Por fim, o método obtém o `Messages` do contexto e o compara a `expectedMessages` declarando que os dois são iguais:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Para comparar os dois `List<Message>` são os mesmos:

* As mensagens são ordenadas por `Id`.
* Pares de mensagens são comparados no `Text` propriedade.

Um método de teste semelhante, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` verifica o resultado da tentativa de excluir uma mensagem que não existe. Nesse caso, as mensagens esperadas no banco de dados devem ser iguais para as mensagens reais após o `DeleteMessageAsync` método é executado. Não deve haver nenhuma alteração para o conteúdo do banco de dados:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testes de unidade dos métodos do modelo de página

Outro conjunto de testes de unidade é responsável por testes de métodos do modelo de página. No aplicativo de mensagem, os modelos de página de índice são encontrados na `IndexModel` classe *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Método de modelo de página | Função |
| ----------------- | -------- |
| `OnGetAsync` | Obtém as mensagens de DAL para a interface do usuário usando o `GetMessagesAsync` método. |
| `OnPostAddMessageAsync` | Se o [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) é válida, as chamadas `AddMessageAsync` para adicionar uma mensagem para o banco de dados. |
| `OnPostDeleteAllMessagesAsync` | Chamadas `DeleteAllMessagesAsync` para excluir todas as mensagens no banco de dados. |
| `OnPostDeleteMessageAsync` | Executa `DeleteMessageAsync` para excluir uma mensagem com o `Id` especificado. |
| `OnPostAnalyzeMessagesAsync` | Se uma ou mais mensagens estão no banco de dados, calcula o número médio de palavras por mensagem. |

Os métodos de modelo de página são testados usando testes de sete na `IndexPageTests` classe (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Os testes de usam o padrão de Act Assert organizar familiar. Esses testes se concentram em:

* Determinando se os métodos seguem o comportamento correto quando o [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) é inválido.
* Confirmar os métodos produzem correto <xref:Microsoft.AspNetCore.Mvc.IActionResult>.
* Verificando-se de que as atribuições de valor de propriedade são feitas corretamente.

Geralmente, esse grupo de testes de simular os métodos da DAL para produzir os dados esperados para a etapa atuar em que um método de modelo de página é executado. Por exemplo, o `GetMessagesAsync` método da `AppDbContext` é simulado para produzir uma saída. Quando este método é executado um método de modelo de página, a simulação retorna o resultado. Os dados não vem do banco de dados. Isso cria condições de teste previsível e confiável para uso a DAL nos testes de modelo de página.

O `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` teste mostra como o `GetMessagesAsync` método é simulado para o modelo de página:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Quando o `OnGetAsync` método é executado na etapa Act, ela chama o modelo de página `GetMessagesAsync` método.

Etapa atuar de teste de unidade (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` modelo de página `OnGetAsync` método (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

O `GetMessagesAsync` método no DAL não retornar o resultado para esta chamada de método. A versão fictícia do método retorna o resultado.

No `Assert` etapa, as mensagens reais (`actualMessages`) são atribuídos a partir de `Messages` propriedade do modelo de página. Uma verificação de tipo também é executada quando as mensagens são atribuídas. As mensagens esperadas e reais são comparadas por seus `Text` propriedades. O teste declara que os dois `List<Message>` instâncias contêm as mesmas mensagens.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Outros testes neste grupo Criar página de objetos de modelo que incluam o <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, o <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, um <xref:Microsoft.AspNetCore.Mvc.ActionContext> para estabelecer a `PageContext`, um `ViewDataDictionary`e um `PageContext`. Elas são úteis na realização de testes. Por exemplo, o aplicativo de mensagens estabelece uma `ModelState` erro com <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> para verificar se válido <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> é retornado quando `OnPostAddMessageAsync` é executado:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Recursos adicionais

* [Teste de unidade em C# no .NET Core usando dotnet test e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Seu código de teste de unidade](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Introdução ao xUnit.net: Usando o .NET Core com a linha de comando do SDK do .NET](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Guia de início rápido Moq](https://github.com/Moq/moq4/wiki/Quickstart)
