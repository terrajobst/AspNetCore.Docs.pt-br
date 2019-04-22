---
title: Examine os métodos Details e Delete de um aplicativo ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o método e a exibição Details do controlador em um aplicativo ASP.NET Core MVC básico.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 056017ea4f4073fa0b1cd747d06775b2a33616cf
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012663"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="3e4bd-103">Examine os métodos Details e Delete de um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e4bd-103">Examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="3e4bd-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3e4bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3e4bd-105">Abra o controlador Movie e examine o método `Details`:</span><span class="sxs-lookup"><span data-stu-id="3e4bd-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="3e4bd-106">O mecanismo de scaffolding MVC que criou este método de ação adiciona um comentário mostrando uma solicitação HTTP que invoca o método.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="3e4bd-107">Nesse caso, é uma solicitação GET com três segmentos de URL, o controlador `Movies`, o método `Details` e um valor `id`.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="3e4bd-108">Lembre-se que esses segmentos são definidos em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="3e4bd-109">O EF facilita a pesquisa de dados usando o método `FirstOrDefaultAsync`.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="3e4bd-110">Um recurso de segurança importante interno do método é que o código verifica se o método de pesquisa encontrou um filme antes de tentar fazer algo com ele.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="3e4bd-111">Por exemplo, um hacker pode introduzir erros no site alterando a URL criada pelos links de `http://localhost:xxxx/Movies/Details/1` para algo como `http://localhost:xxxx/Movies/Details/12345` (ou algum outro valor que não representa um filme real).</span><span class="sxs-lookup"><span data-stu-id="3e4bd-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:xxxx/Movies/Details/1` to something like  `http://localhost:xxxx/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="3e4bd-112">Se você não marcou um filme nulo, o aplicativo gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="3e4bd-113">Examine os métodos `Delete` e `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="3e4bd-114">Observe que o método `HTTP GET Delete` não exclui o filme especificado, mas retorna uma exibição do filme em que você pode enviar (HttpPost) a exclusão.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="3e4bd-115">A execução de uma operação de exclusão em resposta a uma solicitação GET (ou, de fato, a execução de uma operação de edição, criação ou qualquer outra operação que altera dados) abre uma falha de segurança.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="3e4bd-116">O método `[HttpPost]` que exclui os dados é chamado `DeleteConfirmed` para fornecer ao método HTTP POST um nome ou uma assinatura exclusiva.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="3e4bd-117">As duas assinaturas de método são mostradas abaixo:</span><span class="sxs-lookup"><span data-stu-id="3e4bd-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="3e4bd-118">O CLR (Common Language Runtime) exige que os métodos sobrecarregados tenham uma assinatura de parâmetro exclusiva (mesmo nome de método, mas uma lista diferente de parâmetros).</span><span class="sxs-lookup"><span data-stu-id="3e4bd-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="3e4bd-119">No entanto, aqui você precisa de dois métodos `Delete` – um para GET e outro para POST – que têm a mesma assinatura de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="3e4bd-120">(Ambos precisam aceitar um único inteiro como parâmetro.)</span><span class="sxs-lookup"><span data-stu-id="3e4bd-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="3e4bd-121">Há duas abordagens para esse problema e uma delas é fornecer aos métodos nomes diferentes.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="3e4bd-122">Foi isso o que o mecanismo de scaffolding fez no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="3e4bd-123">No entanto, isso apresenta um pequeno problema: o ASP.NET mapeia os segmentos de uma URL para os métodos de ação por nome e, se você renomear um método, o roteamento normalmente não conseguirá encontrar esse método.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="3e4bd-124">A solução é o que você vê no exemplo, que é adicionar o atributo `ActionName("Delete")` ao método `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="3e4bd-125">Esse atributo executa o mapeamento para o sistema de roteamento, de modo que uma URL que inclui /Delete/ para uma solicitação POST encontre o método `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="3e4bd-126">Outra solução alternativa comum para métodos que têm nomes e assinaturas idênticos é alterar artificialmente a assinatura do método POST para incluir um parâmetro extra (não utilizado).</span><span class="sxs-lookup"><span data-stu-id="3e4bd-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="3e4bd-127">Foi isso o que fizemos em uma postagem anterior quando adicionamos o parâmetro `notUsed`.</span><span class="sxs-lookup"><span data-stu-id="3e4bd-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="3e4bd-128">Você pode fazer o mesmo aqui para o método `[HttpPost] Delete`:</span><span class="sxs-lookup"><span data-stu-id="3e4bd-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="3e4bd-129">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="3e4bd-129">Publish to Azure</span></span>

<span data-ttu-id="3e4bd-130">Para obter informações sobre como implantar no Azure, consulte [Tutorial: criar um aplicativo Web do .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="3e4bd-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3e4bd-131">Anterior</span><span class="sxs-lookup"><span data-stu-id="3e4bd-131">Previous</span></span>](validation.md)
