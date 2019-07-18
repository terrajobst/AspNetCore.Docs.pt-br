---
title: Injeção de dependência em exibições no ASP.NET Core
author: ardalis
description: Saiba como o ASP.NET Core permite a injeção de dependência em exibições do MVC.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: 63feea5ddf286dd3e659f3a622cfb0f7451b9bba
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815335"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="2f634-103">Injeção de dependência em exibições no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f634-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="2f634-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2f634-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2f634-105">O ASP.NET Core dá suporte à [injeção de dependência](xref:fundamentals/dependency-injection) em exibições.</span><span class="sxs-lookup"><span data-stu-id="2f634-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="2f634-106">Isso pode ser útil para serviços específicos a uma exibição, como localização ou dados necessários apenas para o preenchimento de elementos de exibição.</span><span class="sxs-lookup"><span data-stu-id="2f634-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="2f634-107">Você deve tentar manter a [separação de interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) entre os controladores e as exibições.</span><span class="sxs-lookup"><span data-stu-id="2f634-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="2f634-108">A maioria dos dados exibida pelas exibições deve ser passada pelo controlador.</span><span class="sxs-lookup"><span data-stu-id="2f634-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="2f634-109">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f634-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="2f634-110">Injeção de configuração</span><span class="sxs-lookup"><span data-stu-id="2f634-110">Configuration injection</span></span>

<span data-ttu-id="2f634-111">Os valores *appsettings.json* podem ser injetados diretamente em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="2f634-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="2f634-112">Exemplo de um arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2f634-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="2f634-113">A sintaxe de `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="2f634-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="2f634-114">Um exemplo do uso de `@inject`:</span><span class="sxs-lookup"><span data-stu-id="2f634-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="2f634-115">Injeção de serviço</span><span class="sxs-lookup"><span data-stu-id="2f634-115">Service injection</span></span>

<span data-ttu-id="2f634-116">Um serviço pode ser injetado em uma exibição usando a diretiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="2f634-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="2f634-117">Você pode observar que o `@inject` adiciona uma propriedade à exibição e preenche a propriedade usando a DI.</span><span class="sxs-lookup"><span data-stu-id="2f634-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="2f634-118">Essa exibição exibe uma lista de instâncias `ToDoItem`, junto com um resumo mostrando estatísticas gerais.</span><span class="sxs-lookup"><span data-stu-id="2f634-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="2f634-119">O resumo é populado com base no `StatisticsService` injetado.</span><span class="sxs-lookup"><span data-stu-id="2f634-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="2f634-120">Esse serviço é registrado para injeção de dependência em `ConfigureServices` em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f634-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="2f634-121">O `StatisticsService` faz alguns cálculos no conjunto de instâncias `ToDoItem`, que ele acessa por meio de um repositório:</span><span class="sxs-lookup"><span data-stu-id="2f634-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="2f634-122">O repositório de exemplo usa uma coleção em memória.</span><span class="sxs-lookup"><span data-stu-id="2f634-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="2f634-123">A implementação mostrada acima (que opera em todos os dados na memória) não é recomendada para conjuntos de dados grandes e acessados remotamente.</span><span class="sxs-lookup"><span data-stu-id="2f634-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="2f634-124">A amostra exibe dados do modelo associado à exibição e o serviço injetado na exibição:</span><span class="sxs-lookup"><span data-stu-id="2f634-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Exibição To Do listando o total de itens, os itens concluídos, a prioridade média e uma lista de tarefas com seus níveis de prioridade e valores boolianos que indica a conclusão.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="2f634-126">Populando os dados de pesquisa</span><span class="sxs-lookup"><span data-stu-id="2f634-126">Populating Lookup Data</span></span>

<span data-ttu-id="2f634-127">A injeção de exibição pode ser útil para popular opções em elementos de interface do usuário, como listas suspensas.</span><span class="sxs-lookup"><span data-stu-id="2f634-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="2f634-128">Considere um formulário de perfil do usuário que inclui opções para especificar o gênero, estado e outras preferências.</span><span class="sxs-lookup"><span data-stu-id="2f634-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="2f634-129">A renderização desse formulário usando uma abordagem MVC padrão exigirá que o controlador solicite serviços de acesso a dados para cada um desses conjuntos de opções e, em seguida, popule um modelo ou `ViewBag` com cada conjunto de opções a ser associado.</span><span class="sxs-lookup"><span data-stu-id="2f634-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="2f634-130">Uma abordagem alternativa injeta os serviços diretamente na exibição para obter as opções.</span><span class="sxs-lookup"><span data-stu-id="2f634-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="2f634-131">Isso minimiza a quantidade de código necessária para o controlador, movendo essa lógica de construção do elemento de exibição para a própria exibição.</span><span class="sxs-lookup"><span data-stu-id="2f634-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="2f634-132">A ação do controlador para exibir um formulário de edição de perfil precisa apenas passar a instância de perfil para o formulário:</span><span class="sxs-lookup"><span data-stu-id="2f634-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="2f634-133">O formulário HTML usado para atualizar essas preferências inclui listas suspensas para três das propriedades:</span><span class="sxs-lookup"><span data-stu-id="2f634-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Atualize a exibição de Perfil com um formulário que permite a entrada de nome, gênero, estado e Cor favorita.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="2f634-135">Essas listas são populadas por um serviço que foi injetado na exibição:</span><span class="sxs-lookup"><span data-stu-id="2f634-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="2f634-136">O `ProfileOptionsService` é um serviço no nível da interface do usuário criado para fornecer apenas os dados necessários para esse formulário:</span><span class="sxs-lookup"><span data-stu-id="2f634-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="2f634-137">Não se esqueça de registrar tipos que você solicita por meio de injeção de dependência no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2f634-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f634-138">Um tipo não registrado gera uma exceção em tempo de execução porque o provedor de serviços é consultado internamente por meio de [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="2f634-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="2f634-139">Substituindo serviços</span><span class="sxs-lookup"><span data-stu-id="2f634-139">Overriding Services</span></span>

<span data-ttu-id="2f634-140">Além de injetar novos serviços, essa técnica também pode ser usada para substituir serviços injetados anteriormente em uma página.</span><span class="sxs-lookup"><span data-stu-id="2f634-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="2f634-141">A figura abaixo mostra todos os campos disponíveis na página usada no primeiro exemplo:</span><span class="sxs-lookup"><span data-stu-id="2f634-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Menu contextual do IntelliSense em um símbolo @ tipado listando os campos Html, Component, StatsService e Url](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="2f634-143">Como você pode ver, os campos padrão incluem `Html`, `Component` e `Url` (bem como o `StatsService` que injetamos).</span><span class="sxs-lookup"><span data-stu-id="2f634-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="2f634-144">Se, para a instância, você desejava substituir os Auxiliares HTML padrão por seus próprios, faça isso com facilidade usando `@inject`:</span><span class="sxs-lookup"><span data-stu-id="2f634-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="2f634-145">Se deseja estender os serviços existentes, basta usar essa técnica herdando da implementação existente ou encapsulando-a com sua própria implementação.</span><span class="sxs-lookup"><span data-stu-id="2f634-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="2f634-146">Veja também</span><span class="sxs-lookup"><span data-stu-id="2f634-146">See Also</span></span>

* <span data-ttu-id="2f634-147">Blog de Simon Timms: [Inserir dados de pesquisa na exibição](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="2f634-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
