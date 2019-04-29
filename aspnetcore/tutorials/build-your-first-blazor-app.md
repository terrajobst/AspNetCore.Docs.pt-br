---
title: Como criar seu primeiro aplicativo Blazor
author: guardrex
description: Crie um aplicativo Blazor passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 310eb211f68076d6f52d6427940e07736d833e5b
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614617"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="f2a0c-103">Como criar seu primeiro aplicativo Blazor</span><span class="sxs-lookup"><span data-stu-id="f2a0c-103">Build your first Blazor app</span></span>

<span data-ttu-id="f2a0c-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f2a0c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="f2a0c-105">Este tutorial mostra como criar um aplicativo com o Razor Components do servidor ou o Blazor do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-105">This tutorial shows you how to build an app with server-side Razor Components or client-side Blazor.</span></span>

<span data-ttu-id="f2a0c-106">Para ter uma experiência de uso do Razor Components no ASP.NET Core do servidor (*recomendado*):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-106">For an experience using server-side ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="f2a0c-107">Siga as diretrizes da *experiência do Razor Components* em <xref:blazor/get-started#server-side-razor-components-experience> para criar um projeto baseado no Razor Components.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-107">Follow the *Razor Components experience* guidance in <xref:blazor/get-started#server-side-razor-components-experience> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="f2a0c-108">Nomeie o projeto `RazorComponents`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-108">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="f2a0c-109">Para ter uma experiência de uso do Blazor:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-109">For an experience using Blazor:</span></span>

* <span data-ttu-id="f2a0c-110">Siga as diretrizes da *experiência do Blazor* em <xref:blazor/get-started#client-side-blazor-experience> para criar um projeto baseado no Blazor.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-110">Follow the *Blazor experience* guidance in <xref:blazor/get-started#client-side-blazor-experience> to create a Blazor-based project.</span></span>
* <span data-ttu-id="f2a0c-111">Nomeie o projeto `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-111">Name the project `Blazor`.</span></span>

<span data-ttu-id="f2a0c-112">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f2a0c-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="f2a0c-113">Conheça os pré-requisitos nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-113">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="f2a0c-114">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="f2a0c-114">Build components</span></span>

1. <span data-ttu-id="f2a0c-115">Navegue até cada uma das três páginas do aplicativo na pasta *Components/Pages* (*Páginas* no Blazor): Início, Contador e Buscar dados.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-115">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="f2a0c-116">Essas páginas são implementadas por arquivos de Componente do Razor: *Index.razor*, *Counter.razor* e *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-116">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="f2a0c-117">(O Blazor continua a usar a extensão de arquivo *.cshtml*: *Index.cshtml*, *Counter.cshtml* e *FetchData.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="f2a0c-117">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="f2a0c-118">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-118">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f2a0c-119">A incrementação de um contador em uma página da Web normalmente exige JavaScript, mas o Blazor fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-119">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="f2a0c-120">Examine a implementação do componente Counter no arquivo *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-120">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="f2a0c-121">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-121">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="f2a0c-122">A interface do usuário do componente Counter é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-122">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="f2a0c-123">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="f2a0c-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="f2a0c-124">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="f2a0c-125">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="f2a0c-126">Os membros da classe de componente são definidos em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-126">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="f2a0c-127">No bloco `@functions`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-127">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="f2a0c-128">Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="f2a0c-129">Quando o botão **Clique aqui** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="f2a0c-130">O manipulador `onclick` registrado do componente Counter é chamado (o método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="f2a0c-130">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="f2a0c-131">O componente Counter regenera sua árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-131">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="f2a0c-132">A nova árvore de renderização é comparada à anterior.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="f2a0c-133">Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="f2a0c-134">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="f2a0c-135">Modifique a lógica de C# do componente Counter para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-135">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="f2a0c-136">Recompile e execute o aplicativo para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="f2a0c-137">Selecione o botão **Clique aqui**, e o contador é incrementado em dois.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-137">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="f2a0c-138">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="f2a0c-138">Use components</span></span>

<span data-ttu-id="f2a0c-139">Inclua um componente em outro componente usando uma sintaxe semelhante a HTML.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-139">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="f2a0c-140">Adicione o componente Counter ao componente Index (página inicial) do aplicativo adicionando um elemento `<Counter />` ao componente Index.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-140">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="f2a0c-141">Se você estiver usando Blazor para essa experiência, um componente Survey Prompt (elemento `<SurveyPrompt>`) estará no componente Index.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-141">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="f2a0c-142">Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter>`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-142">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="f2a0c-143">*Components/Pages/Index.razor* (*Pages/Index.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-143">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="f2a0c-144">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-144">Rebuild and run the app.</span></span> <span data-ttu-id="f2a0c-145">A página inicial tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-145">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="f2a0c-146">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="f2a0c-146">Component parameters</span></span>

<span data-ttu-id="f2a0c-147">Componentes também podem ter parâmetros.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-147">Components can also have parameters.</span></span> <span data-ttu-id="f2a0c-148">Os parâmetros do componente são definidos usando propriedades não públicas na classe de componente decorada com `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-148">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="f2a0c-149">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-149">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="f2a0c-150">Atualizar o código C# do componente `@functions`:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-150">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="f2a0c-151">Adicione uma propriedade `IncrementAmount` decorada com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-151">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="f2a0c-152">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-152">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="f2a0c-153">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-153">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="f2a0c-154">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente Home usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-154">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="f2a0c-155">Defina o valor para incrementar o contador em 10.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-155">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="f2a0c-156">*Components/Pages/Index.razor* (*Pages/Index.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-156">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="f2a0c-157">Recarregue a página inicial.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-157">Reload the Home page.</span></span> <span data-ttu-id="f2a0c-158">O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-158">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="f2a0c-159">O contador na página Contador aumenta em um.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-159">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="f2a0c-160">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="f2a0c-160">Route to components</span></span>

<span data-ttu-id="f2a0c-161">A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente é um ponto de extremidade de roteamento.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-161">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="f2a0c-162">O componente Counter manipula solicitações enviadas para `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-162">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="f2a0c-163">Sem a diretiva `@page`, o componente não trata as solicitações roteadas, mas o componente ainda pode ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-163">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="f2a0c-164">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="f2a0c-164">Dependency injection</span></span>

<span data-ttu-id="f2a0c-165">Os serviços registrados no contêiner do serviço de aplicativo estão disponíveis para componentes por meio da [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f2a0c-165">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f2a0c-166">Injete os serviços em um componente usando a diretiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-166">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="f2a0c-167">Examine as diretivas do componente FetchData no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-167">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="f2a0c-168">No aplicativo de exemplo Razor Components, o serviço `WeatherForecastService` é registrado como um [singleton](xref:fundamentals/dependency-injection#service-lifetimes). Dessa maneira, uma instância do serviço ficará disponível em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-168">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="f2a0c-169">A diretiva `@inject` é usada para injetar a instância do serviço `WeatherForecastService` no componente.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-169">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="f2a0c-170">*Components/Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-170">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="f2a0c-171">O componente FetchData usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-171">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="f2a0c-172">Na versão Blazor do aplicativo de exemplo, `HttpClient` é injetado para obter dados de previsão de tempo do arquivo *weather.json* na pasta *wwwroot/sample-data*:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-172">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="f2a0c-173">*Pages/FetchData.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-173">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="f2a0c-174">Nos dois aplicativos de exemplo, um loop [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) é usado para renderizar cada instância de previsão como uma linha na tabela de dados meteorológicos:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-174">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="f2a0c-175">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="f2a0c-175">Build a todo list</span></span>

<span data-ttu-id="f2a0c-176">Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-176">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="f2a0c-177">Adicione um arquivo vazio ao aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-177">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="f2a0c-178">Para a experiência do Razor Components, adicione um arquivo *Todo.razor* à pasta *Components/Pages*.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-178">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="f2a0c-179">Para a experiência do Blazor, adicione um arquivo *Todo.cshtml* à pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-179">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="f2a0c-180">Forneça a marcação inicial do componente:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-180">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="f2a0c-181">Adicione o componente Todo à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-181">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="f2a0c-182">O componente NavMenu (*Components/Shared/NavMenu.razor*  ou *Shared/NavMenu.cshtml* no Blazor) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-182">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="f2a0c-183">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-183">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="f2a0c-184">Para obter mais informações, consulte <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-184">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="f2a0c-185">Adicione um `<NavLink>` ao componente Todo incluindo a marcação de item de lista a seguir abaixo dos itens de lista existentes no arquivo *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-185">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="f2a0c-186">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-186">Rebuild and run the app.</span></span> <span data-ttu-id="f2a0c-187">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente Todo está funcionando.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-187">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="f2a0c-188">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-188">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="f2a0c-189">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-189">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="f2a0c-190">Retorne ao componente Todo (*Components/Pages/Todo.razor* ou *Pages/Todo.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-190">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="f2a0c-191">Adicione um campo para as tarefas pendentes em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-191">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="f2a0c-192">O componente Todo usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-192">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="f2a0c-193">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-193">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="f2a0c-194">O aplicativo requer os elementos de interface do usuário para adicionar tarefas pendentes à lista.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-194">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="f2a0c-195">Adicione uma entrada de texto e um botão abaixo da lista:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-195">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="f2a0c-196">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-196">Rebuild and run the app.</span></span> <span data-ttu-id="f2a0c-197">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-197">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="f2a0c-198">Adicione um método `AddTodo` ao componente Todo e registre-o para cliques de botão usando o atributo `onclick`:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-198">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="f2a0c-199">O método `AddTodo` em C# é chamado quando o botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-199">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="f2a0c-200">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` e associe-o ao valor da próxima entrada de texto usando o atributo `bind`:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-200">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="f2a0c-201">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-201">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="f2a0c-202">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-202">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="f2a0c-203">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-203">Rebuild and run the app.</span></span> <span data-ttu-id="f2a0c-204">Adicione algumas tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-204">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="f2a0c-205">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-205">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="f2a0c-206">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-206">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="f2a0c-207">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="f2a0c-207">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="f2a0c-208">Para verificar se esses valores estão associados, atualize o cabeçalho `<h1>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="f2a0c-208">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="f2a0c-209">Retorne ao componente Todo concluído (*Components/Pages/Todo.razor* ou *Pages/Todo.cshtml* no Blazor):</span><span class="sxs-lookup"><span data-stu-id="f2a0c-209">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="f2a0c-210">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-210">Rebuild and run the app.</span></span> <span data-ttu-id="f2a0c-211">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-211">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="f2a0c-212">Publicar e implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f2a0c-212">Publish and deploy the app</span></span>

<span data-ttu-id="f2a0c-213">Para publicar o aplicativo, confira <xref:host-and-deploy/blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="f2a0c-213">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
