---
title: Injeção de dependência em exibições no ASP.NET Core
author: ardalis
description: Saiba como o ASP.NET Core permite a injeção de dependência em exibições do MVC.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: dfadafe9ebb5799b45ef68653f20c5fc1a2506b5
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410554"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="48244-103">Injeção de dependência em exibições no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48244-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="48244-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="48244-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="48244-105">O ASP.NET Core dá suporte à [injeção de dependência](xref:fundamentals/dependency-injection) em exibições.</span><span class="sxs-lookup"><span data-stu-id="48244-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="48244-106">Isso pode ser útil para serviços específicos a uma exibição, como localização ou dados necessários apenas para o preenchimento de elementos de exibição.</span><span class="sxs-lookup"><span data-stu-id="48244-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="48244-107">Você deve tentar manter a [separação de interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) entre os controladores e as exibições.</span><span class="sxs-lookup"><span data-stu-id="48244-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="48244-108">A maioria dos dados exibida pelas exibições deve ser passada pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="48244-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="48244-109">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48244-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="a-simple-example"></a><span data-ttu-id="48244-110">Um exemplo simples</span><span class="sxs-lookup"><span data-stu-id="48244-110">A Simple Example</span></span>

<span data-ttu-id="48244-111">Injete um serviço em uma exibição usando a diretiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="48244-111">You can inject a service into a view using the `@inject` directive.</span></span> <span data-ttu-id="48244-112">Considere `@inject` como a adição de uma propriedade à exibição e o preenchimento da propriedade usando a DI.</span><span class="sxs-lookup"><span data-stu-id="48244-112">You can think of `@inject` as adding a property to your view, and populating the property using DI.</span></span>

<span data-ttu-id="48244-113">A sintaxe de `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="48244-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="48244-114">Um exemplo de `@inject` em ação:</span><span class="sxs-lookup"><span data-stu-id="48244-114">An example of `@inject` in action:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="48244-115">Essa exibição exibe uma lista de instâncias `ToDoItem`, junto com um resumo mostrando estatísticas gerais.</span><span class="sxs-lookup"><span data-stu-id="48244-115">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="48244-116">O resumo é populado com base no `StatisticsService` injetado.</span><span class="sxs-lookup"><span data-stu-id="48244-116">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="48244-117">Esse serviço é registrado para injeção de dependência em `ConfigureServices` em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="48244-117">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="48244-118">O `StatisticsService` faz alguns cálculos no conjunto de instâncias `ToDoItem`, que ele acessa por meio de um repositório:</span><span class="sxs-lookup"><span data-stu-id="48244-118">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="48244-119">O repositório de exemplo usa uma coleção em memória.</span><span class="sxs-lookup"><span data-stu-id="48244-119">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="48244-120">A implementação mostrada acima (que opera em todos os dados na memória) não é recomendada para conjuntos de dados grandes e acessados remotamente.</span><span class="sxs-lookup"><span data-stu-id="48244-120">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="48244-121">A amostra exibe dados do modelo associado à exibição e o serviço injetado na exibição:</span><span class="sxs-lookup"><span data-stu-id="48244-121">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Exibição To Do listando o total de itens, os itens concluídos, a prioridade média e uma lista de tarefas com seus níveis de prioridade e valores boolianos que indica a conclusão.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="48244-123">Populando os dados de pesquisa</span><span class="sxs-lookup"><span data-stu-id="48244-123">Populating Lookup Data</span></span>

<span data-ttu-id="48244-124">A injeção de exibição pode ser útil para popular opções em elementos de interface do usuário, como listas suspensas.</span><span class="sxs-lookup"><span data-stu-id="48244-124">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="48244-125">Considere um formulário de perfil do usuário que inclui opções para especificar o gênero, estado e outras preferências.</span><span class="sxs-lookup"><span data-stu-id="48244-125">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="48244-126">A renderização desse formulário usando uma abordagem MVC padrão exigirá que o controlador solicite serviços de acesso a dados para cada um desses conjuntos de opções e, em seguida, popule um modelo ou `ViewBag` com cada conjunto de opções a ser associado.</span><span class="sxs-lookup"><span data-stu-id="48244-126">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="48244-127">Uma abordagem alternativa injeta os serviços diretamente na exibição para obter as opções.</span><span class="sxs-lookup"><span data-stu-id="48244-127">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="48244-128">Isso minimiza a quantidade de código necessária para o controlador, movendo essa lógica de construção do elemento de exibição para a própria exibição.</span><span class="sxs-lookup"><span data-stu-id="48244-128">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="48244-129">A ação do controlador para exibir um formulário de edição de perfil precisa apenas passar a instância de perfil para o formulário:</span><span class="sxs-lookup"><span data-stu-id="48244-129">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="48244-130">O formulário HTML usado para atualizar essas preferências inclui listas suspensas para três das propriedades:</span><span class="sxs-lookup"><span data-stu-id="48244-130">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Atualize a exibição de Perfil com um formulário que permite a entrada de nome, gênero, estado e Cor favorita.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="48244-132">Essas listas são populadas por um serviço que foi injetado na exibição:</span><span class="sxs-lookup"><span data-stu-id="48244-132">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="48244-133">O `ProfileOptionsService` é um serviço no nível da interface do usuário criado para fornecer apenas os dados necessários para esse formulário:</span><span class="sxs-lookup"><span data-stu-id="48244-133">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="48244-134">Não se esqueça de registrar tipos que você solicita por meio de injeção de dependência no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="48244-134">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="48244-135">Um tipo não registrado gera uma exceção em tempo de execução porque o provedor de serviços é consultado internamente por meio de [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="48244-135">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="48244-136">Substituindo serviços</span><span class="sxs-lookup"><span data-stu-id="48244-136">Overriding Services</span></span>

<span data-ttu-id="48244-137">Além de injetar novos serviços, essa técnica também pode ser usada para substituir serviços injetados anteriormente em uma página.</span><span class="sxs-lookup"><span data-stu-id="48244-137">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="48244-138">A figura abaixo mostra todos os campos disponíveis na página usada no primeiro exemplo:</span><span class="sxs-lookup"><span data-stu-id="48244-138">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Menu contextual do IntelliSense em um símbolo @ tipado listando os campos Html, Component, StatsService e Url](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="48244-140">Como você pode ver, os campos padrão incluem `Html`, `Component` e `Url` (bem como o `StatsService` que injetamos).</span><span class="sxs-lookup"><span data-stu-id="48244-140">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="48244-141">Se, para a instância, você desejava substituir os Auxiliares HTML padrão por seus próprios, faça isso com facilidade usando `@inject`:</span><span class="sxs-lookup"><span data-stu-id="48244-141">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="48244-142">Se deseja estender os serviços existentes, basta usar essa técnica herdando da implementação existente ou encapsulando-a com sua própria implementação.</span><span class="sxs-lookup"><span data-stu-id="48244-142">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="48244-143">Consulte também</span><span class="sxs-lookup"><span data-stu-id="48244-143">See Also</span></span>

* <span data-ttu-id="48244-144">Blog de Simon Timms: [Inserir dados de pesquisa na exibição](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="48244-144">Simon Timms Blog: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
