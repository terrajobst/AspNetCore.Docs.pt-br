---
title: Examine os métodos Details e Delete de um aplicativo ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o método e a exibição Details do controlador em um aplicativo ASP.NET Core MVC básico.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803377"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Examine os métodos Details e Delete de um aplicativo ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Abra o controlador Movie e examine o método `Details`:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

O mecanismo de scaffolding MVC que criou este método de ação adiciona um comentário mostrando uma solicitação HTTP que invoca o método. Nesse caso, é uma solicitação GET com três segmentos de URL, o controlador `Movies`, o método `Details` e um valor `id`. Lembre-se que esses segmentos são definidos em *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

O EF facilita a pesquisa de dados usando o método `FirstOrDefaultAsync`. Um recurso de segurança importante interno do método é que o código verifica se o método de pesquisa encontrou um filme antes de tentar fazer algo com ele. Por exemplo, um hacker pode introduzir erros no site alterando a URL criada pelos links de `http://localhost:{PORT}/Movies/Details/1` para algo como `http://localhost:{PORT}/Movies/Details/12345` (ou algum outro valor que não representa um filme real). Se você não marcou um filme nulo, o aplicativo gerará uma exceção.

Examine os métodos `Delete` e `DeleteConfirmed`.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Observe que o método `HTTP GET Delete` não exclui o filme especificado, mas retorna uma exibição do filme em que você pode enviar (HttpPost) a exclusão. A execução de uma operação de exclusão em resposta a uma solicitação GET (ou, de fato, a execução de uma operação de edição, criação ou qualquer outra operação que altera dados) abre uma falha de segurança.

O método `[HttpPost]` que exclui os dados é chamado `DeleteConfirmed` para fornecer ao método HTTP POST um nome ou uma assinatura exclusiva. As duas assinaturas de método são mostradas abaixo:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

O CLR (Common Language Runtime) exige que os métodos sobrecarregados tenham uma assinatura de parâmetro exclusiva (mesmo nome de método, mas uma lista diferente de parâmetros). No entanto, aqui você precisa de dois métodos `Delete` – um para GET e outro para POST – que têm a mesma assinatura de parâmetro. (Ambos precisam aceitar um único inteiro como parâmetro.)

Há duas abordagens para esse problema e uma delas é fornecer aos métodos nomes diferentes. Foi isso o que o mecanismo de scaffolding fez no exemplo anterior. No entanto, isso apresenta um pequeno problema: o ASP.NET mapeia os segmentos de URL para os métodos de ação por nome e se você renomear um método, o roteamento normalmente não conseguirá encontrar esse método. A solução é o que você vê no exemplo, que é adicionar o atributo `ActionName("Delete")` ao método `DeleteConfirmed`. Esse atributo executa o mapeamento para o sistema de roteamento, de modo que uma URL que inclui /Delete/ para uma solicitação POST encontre o método `DeleteConfirmed`.

Outra solução alternativa comum para métodos que têm nomes e assinaturas idênticos é alterar artificialmente a assinatura do método POST para incluir um parâmetro extra (não utilizado). Foi isso o que fizemos em uma postagem anterior quando adicionamos o parâmetro `notUsed`. Você pode fazer o mesmo aqui para o método `[HttpPost] Delete`:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Publicar no Azure

Para obter informações de como implantar no Azure, confira [Tutorial: Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Anterior](validation.md)
