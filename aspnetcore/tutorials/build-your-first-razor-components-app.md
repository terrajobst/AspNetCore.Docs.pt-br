---
title: Compilar seu primeiro aplicativo com Razor Components
author: guardrex
description: Compile passo a passo um aplicativo com Razor Components e aprenda os conceitos básicos do Razor Components.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 697c4659bcc9952ffe9868fe9b3c0d28019bc369
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468770"
---
# <a name="build-your-first-razor-components-app"></a><span data-ttu-id="84e6d-103">Compilar seu primeiro aplicativo com Razor Components</span><span class="sxs-lookup"><span data-stu-id="84e6d-103">Build your first Razor Components app</span></span>

<span data-ttu-id="84e6d-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="84e6d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="84e6d-105">Este tutorial mostra como compilar um aplicativo com Razor Components e demonstra os conceitos básicos do Razor Components.</span><span class="sxs-lookup"><span data-stu-id="84e6d-105">This tutorial shows you how to build an app with Razor Components and demonstrates basic Razor Components concepts.</span></span> <span data-ttu-id="84e6d-106">Aproveite melhor esse tutorial usando um projeto com base em Razor Components (com suporte no .NET Core 3.0 ou posterior) ou usando um projeto com base em Blazor (com suporte em uma versão futura do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="84e6d-106">You can enjoy this tutorial using either a Razor Components-based project (supported in .NET Core 3.0 or later) or using a Blazor-based project (supported in a future release of .NET Core).</span></span>

<span data-ttu-id="84e6d-107">Para ter uma experiência de uso do Razor Components no ASP.NET Core (*recomendado*):</span><span class="sxs-lookup"><span data-stu-id="84e6d-107">For an experience using ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="84e6d-108">Siga as orientações em <xref:razor-components/get-started> para criar um projeto com base no Razor Components.</span><span class="sxs-lookup"><span data-stu-id="84e6d-108">Follow the guidance in <xref:razor-components/get-started> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="84e6d-109">Nomeie o projeto `RazorComponents`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-109">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="84e6d-110">Para ter uma experiência de uso do Blazor:</span><span class="sxs-lookup"><span data-stu-id="84e6d-110">For an experience using Blazor:</span></span>

* <span data-ttu-id="84e6d-111">Siga as orientações em <xref:spa/blazor/get-started> para criar um projeto com base no Blazor.</span><span class="sxs-lookup"><span data-stu-id="84e6d-111">Follow the guidance in <xref:spa/blazor/get-started> to create a Blazor-based project.</span></span>
* <span data-ttu-id="84e6d-112">Nomeie o projeto `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-112">Name the project `Blazor`.</span></span>

<span data-ttu-id="84e6d-113">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="84e6d-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="84e6d-114">Conheça os pré-requisitos nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="84e6d-114">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="84e6d-115">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="84e6d-115">Build components</span></span>

1. <span data-ttu-id="84e6d-116">Navegue até cada uma das três páginas do aplicativo na pasta *Components/Pages* (*Páginas* no Blazor): Início, Contador e Buscar dados.</span><span class="sxs-lookup"><span data-stu-id="84e6d-116">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="84e6d-117">Essas páginas são implementadas por arquivos de Componente do Razor: *Index.razor*, *Counter.razor* e *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="84e6d-117">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="84e6d-118">(O Blazor continua a usar a extensão de arquivo *.cshtml*: *Index.cshtml*, *Counter.cshtml* e *FetchData.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="84e6d-118">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="84e6d-119">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="84e6d-119">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="84e6d-120">A incrementação de um contador em uma página da Web normalmente exige JavaScript, mas o Razor Components fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="84e6d-120">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

1. <span data-ttu-id="84e6d-121">Examine a implementação do componente Counter no arquivo *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="84e6d-121">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="84e6d-122">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="84e6d-122">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="84e6d-123">A interface do usuário do componente Counter é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="84e6d-123">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="84e6d-124">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="84e6d-124">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="84e6d-125">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="84e6d-125">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="84e6d-126">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-126">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="84e6d-127">Os membros da classe de componente são definidos em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-127">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="84e6d-128">No bloco `@functions`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="84e6d-128">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="84e6d-129">Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="84e6d-129">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="84e6d-130">Quando o botão **Clique aqui** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="84e6d-130">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="84e6d-131">O manipulador `onclick` registrado do componente Counter é chamado (o método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="84e6d-131">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="84e6d-132">O componente Counter regenera sua árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="84e6d-132">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="84e6d-133">A nova árvore de renderização é comparada à anterior.</span><span class="sxs-lookup"><span data-stu-id="84e6d-133">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="84e6d-134">Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="84e6d-134">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="84e6d-135">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="84e6d-135">The displayed count is updated.</span></span>

1. <span data-ttu-id="84e6d-136">Modifique a lógica de C# do componente Counter para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="84e6d-136">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="84e6d-137">Recompile e execute o aplicativo para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="84e6d-137">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="84e6d-138">Selecione o botão **Clique aqui**, e o contador é incrementado em dois.</span><span class="sxs-lookup"><span data-stu-id="84e6d-138">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="84e6d-139">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="84e6d-139">Use components</span></span>

<span data-ttu-id="84e6d-140">Inclua um componente em outro componente usando uma sintaxe semelhante a HTML.</span><span class="sxs-lookup"><span data-stu-id="84e6d-140">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="84e6d-141">Adicione o componente Counter ao componente Index (página inicial) do aplicativo adicionando um elemento `<Counter />` ao componente Index.</span><span class="sxs-lookup"><span data-stu-id="84e6d-141">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="84e6d-142">Se você estiver usando Blazor para essa experiência, um componente Survey Prompt (elemento `<SurveyPrompt>`) estará no componente Index.</span><span class="sxs-lookup"><span data-stu-id="84e6d-142">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="84e6d-143">Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter>`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-143">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="84e6d-144">*Components/Pages/Index.razor* (*Pages/Index.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="84e6d-144">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="84e6d-145">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-145">Rebuild and run the app.</span></span> <span data-ttu-id="84e6d-146">A página inicial tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="84e6d-146">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="84e6d-147">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="84e6d-147">Component parameters</span></span>

<span data-ttu-id="84e6d-148">Componentes também podem ter parâmetros.</span><span class="sxs-lookup"><span data-stu-id="84e6d-148">Components can also have parameters.</span></span> <span data-ttu-id="84e6d-149">Os parâmetros do componente são definidos usando propriedades não públicas na classe de componente decorada com `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-149">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="84e6d-150">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="84e6d-150">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="84e6d-151">Atualizar o código C# do componente `@functions`:</span><span class="sxs-lookup"><span data-stu-id="84e6d-151">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="84e6d-152">Adicione uma propriedade `IncrementAmount` decorada com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-152">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="84e6d-153">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-153">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="84e6d-154">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="84e6d-154">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="84e6d-155">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente Home usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-155">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="84e6d-156">Defina o valor para incrementar o contador em 10.</span><span class="sxs-lookup"><span data-stu-id="84e6d-156">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="84e6d-157">*Components/Pages/Index.razor* (*Pages/Index.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="84e6d-157">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="84e6d-158">Recarregue a página inicial.</span><span class="sxs-lookup"><span data-stu-id="84e6d-158">Reload the Home page.</span></span> <span data-ttu-id="84e6d-159">O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="84e6d-159">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="84e6d-160">O contador na página Contador aumenta em um.</span><span class="sxs-lookup"><span data-stu-id="84e6d-160">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="84e6d-161">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="84e6d-161">Route to components</span></span>

<span data-ttu-id="84e6d-162">A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente é um ponto de extremidade de roteamento.</span><span class="sxs-lookup"><span data-stu-id="84e6d-162">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="84e6d-163">O componente Counter manipula solicitações enviadas para `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-163">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="84e6d-164">Sem a diretiva `@page`, o componente não trata as solicitações roteadas, mas o componente ainda pode ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="84e6d-164">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="84e6d-165">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="84e6d-165">Dependency injection</span></span>

<span data-ttu-id="84e6d-166">Os serviços registrados no contêiner do serviço de aplicativo estão disponíveis para componentes por meio da [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="84e6d-166">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="84e6d-167">Injete os serviços em um componente usando a diretiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-167">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="84e6d-168">Examine as diretivas do componente FetchData no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-168">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="84e6d-169">No aplicativo de exemplo Razor Components, o serviço `WeatherForecastService` é registrado como um [singleton](xref:fundamentals/dependency-injection#service-lifetimes). Dessa maneira, uma instância do serviço ficará disponível em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-169">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="84e6d-170">A diretiva `@inject` é usada para injetar a instância do serviço `WeatherForecastService` no componente.</span><span class="sxs-lookup"><span data-stu-id="84e6d-170">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="84e6d-171">*Components/Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="84e6d-171">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="84e6d-172">O componente FetchData usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="84e6d-172">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="84e6d-173">Na versão Blazor do aplicativo de exemplo, `HttpClient` é injetado para obter dados de previsão de tempo do arquivo *weather.json* na pasta *wwwroot/sample-data*:</span><span class="sxs-lookup"><span data-stu-id="84e6d-173">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="84e6d-174">*Pages/FetchData.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="84e6d-174">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="84e6d-175">Nos dois aplicativos de exemplo, um loop [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) é usado para renderizar cada instância de previsão como uma linha na tabela de dados meteorológicos:</span><span class="sxs-lookup"><span data-stu-id="84e6d-175">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="84e6d-176">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="84e6d-176">Build a todo list</span></span>

<span data-ttu-id="84e6d-177">Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="84e6d-177">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="84e6d-178">Adicione um arquivo vazio ao aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="84e6d-178">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="84e6d-179">Para a experiência do Razor Components, adicione um arquivo *Todo.razor* à pasta *Components/Pages*.</span><span class="sxs-lookup"><span data-stu-id="84e6d-179">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="84e6d-180">Para a experiência do Blazor, adicione um arquivo *Todo.cshtml* à pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="84e6d-180">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="84e6d-181">Forneça a marcação inicial do componente:</span><span class="sxs-lookup"><span data-stu-id="84e6d-181">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="84e6d-182">Adicione o componente Todo à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="84e6d-182">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="84e6d-183">O componente NavMenu (*Components/Shared/NavMenu.razor*  ou *Shared/NavMenu.cshtml* no Blazor) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-183">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="84e6d-184">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-184">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="84e6d-185">Para obter mais informações, consulte <xref:razor-components/layouts>.</span><span class="sxs-lookup"><span data-stu-id="84e6d-185">For more information, see <xref:razor-components/layouts>.</span></span>

   <span data-ttu-id="84e6d-186">Adicione um `<NavLink>` ao componente Todo incluindo a marcação de item de lista a seguir abaixo dos itens de lista existentes no arquivo *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="84e6d-186">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="84e6d-187">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-187">Rebuild and run the app.</span></span> <span data-ttu-id="84e6d-188">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente Todo está funcionando.</span><span class="sxs-lookup"><span data-stu-id="84e6d-188">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="84e6d-189">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="84e6d-189">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="84e6d-190">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="84e6d-190">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="84e6d-191">Retorne ao componente Todo (*Components/Pages/Todo.razor* ou *Pages/Todo.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="84e6d-191">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="84e6d-192">Adicione um campo para as tarefas pendentes em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-192">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="84e6d-193">O componente Todo usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="84e6d-193">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="84e6d-194">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista.</span><span class="sxs-lookup"><span data-stu-id="84e6d-194">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="84e6d-195">O aplicativo requer os elementos de interface do usuário para adicionar tarefas pendentes à lista.</span><span class="sxs-lookup"><span data-stu-id="84e6d-195">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="84e6d-196">Adicione uma entrada de texto e um botão abaixo da lista:</span><span class="sxs-lookup"><span data-stu-id="84e6d-196">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="84e6d-197">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-197">Rebuild and run the app.</span></span> <span data-ttu-id="84e6d-198">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="84e6d-198">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="84e6d-199">Adicione um método `AddTodo` ao componente Todo e registre-o para cliques de botão usando o atributo `onclick`:</span><span class="sxs-lookup"><span data-stu-id="84e6d-199">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="84e6d-200">O método `AddTodo` em C# é chamado quando o botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="84e6d-200">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="84e6d-201">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` e associe-o ao valor da próxima entrada de texto usando o atributo `bind`:</span><span class="sxs-lookup"><span data-stu-id="84e6d-201">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="84e6d-202">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="84e6d-202">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="84e6d-203">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="84e6d-203">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="84e6d-204">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-204">Rebuild and run the app.</span></span> <span data-ttu-id="84e6d-205">Adicione algumas tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="84e6d-205">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="84e6d-206">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="84e6d-206">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="84e6d-207">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="84e6d-207">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="84e6d-208">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="84e6d-208">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="84e6d-209">Para verificar se esses valores estão associados, atualize o cabeçalho `<h1>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="84e6d-209">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="84e6d-210">Retorne ao componente Todo concluído (*Components/Pages/Todo.razor* ou *Pages/Todo.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="84e6d-210">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="84e6d-211">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e6d-211">Rebuild and run the app.</span></span> <span data-ttu-id="84e6d-212">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="84e6d-212">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="84e6d-213">Publicar e implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="84e6d-213">Publish and deploy the app</span></span>

<span data-ttu-id="84e6d-214">Para publicar o aplicativo, confira <xref:host-and-deploy/razor-components-blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="84e6d-214">To publish the app, see <xref:host-and-deploy/razor-components-blazor/index>.</span></span>
