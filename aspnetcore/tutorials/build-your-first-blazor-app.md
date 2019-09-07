---
title: Como criar seu primeiro aplicativo Blazor
author: guardrex
description: Crie um aplicativo Blazor passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: ea1111f43b6b8b4f47061056e8ad8d505f92dba6
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800484"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="891b1-103">Como criar seu primeiro aplicativo Blazor</span><span class="sxs-lookup"><span data-stu-id="891b1-103">Build your first Blazor app</span></span>

<span data-ttu-id="891b1-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="891b1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="891b1-105">Este tutorial mostra como criar e modificar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="891b1-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="891b1-106">Siga as orientações no artigo <xref:blazor/get-started> para criar um projeto Blazor para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="891b1-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="891b1-107">Nomeie o projeto como *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="891b1-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="891b1-108">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="891b1-108">Build components</span></span>

1. <span data-ttu-id="891b1-109">Navegue até cada uma das três páginas do aplicativo na pasta *Pages*: Início, Contador e Buscar dados.</span><span class="sxs-lookup"><span data-stu-id="891b1-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="891b1-110">Essas páginas são implementadas pelos arquivos de componente Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="891b1-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="891b1-111">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="891b1-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="891b1-112">A incrementação de um contador em uma página da Web normalmente exige JavaScript, mas o Blazor fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="891b1-112">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="891b1-113">Examine a implementação do componente `Counter` no arquivo *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="891b1-113">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="891b1-114">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="891b1-114">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="891b1-115">A interface do usuário do componente `Counter` é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="891b1-115">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="891b1-116">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="891b1-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="891b1-117">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="891b1-117">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="891b1-118">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="891b1-118">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="891b1-119">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="891b1-119">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="891b1-120">No bloco `@code`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="891b1-120">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="891b1-121">Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="891b1-121">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="891b1-122">Quando o botão **Clique aqui** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="891b1-122">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="891b1-123">O manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="891b1-123">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="891b1-124">O componente `Counter` regenera sua árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="891b1-124">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="891b1-125">A nova árvore de renderização é comparada à anterior.</span><span class="sxs-lookup"><span data-stu-id="891b1-125">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="891b1-126">Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="891b1-126">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="891b1-127">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="891b1-127">The displayed count is updated.</span></span>

1. <span data-ttu-id="891b1-128">Modifique a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="891b1-128">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="891b1-129">Recompile e execute o aplicativo para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="891b1-129">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="891b1-130">Selecione o botão **Clique aqui**.</span><span class="sxs-lookup"><span data-stu-id="891b1-130">Select the **Click me** button.</span></span> <span data-ttu-id="891b1-131">O contador incrementa em dois.</span><span class="sxs-lookup"><span data-stu-id="891b1-131">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="891b1-132">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="891b1-132">Use components</span></span>

<span data-ttu-id="891b1-133">Inclua um componente em outro componente usando uma sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="891b1-133">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="891b1-134">Adicione o componente `Counter` no componente `Index` do aplicativo adicionando um elemento `<Counter />` ao componente `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="891b1-134">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="891b1-135">Se você estiver usando Blazor no lado do cliente para essa experiência, um componente `SurveyPrompt` é usado pelo componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="891b1-135">If you're using Blazor client-side for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="891b1-136">Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="891b1-136">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="891b1-137">Se você estiver usando um aplicativo Blazor do lado do servidor para esta experiência, adicione o elemento `<Counter />` ao componente `Index`:</span><span class="sxs-lookup"><span data-stu-id="891b1-137">If you're using a Blazor server-side app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="891b1-138">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="891b1-138">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="891b1-139">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-139">Rebuild and run the app.</span></span> <span data-ttu-id="891b1-140">O componente `Index` tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="891b1-140">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="891b1-141">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="891b1-141">Component parameters</span></span>

<span data-ttu-id="891b1-142">Componentes também podem ter parâmetros.</span><span class="sxs-lookup"><span data-stu-id="891b1-142">Components can also have parameters.</span></span> <span data-ttu-id="891b1-143">Os parâmetros de componente são definidos usando propriedades públicas na classe de componente `[Parameter]` com o atributo.</span><span class="sxs-lookup"><span data-stu-id="891b1-143">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="891b1-144">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="891b1-144">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="891b1-145">Atualizar o código C# do componente `@code`:</span><span class="sxs-lookup"><span data-stu-id="891b1-145">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="891b1-146">Adicione uma propriedade `IncrementAmount` pública com o `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="891b1-146">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="891b1-147">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="891b1-147">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="891b1-148">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="891b1-148">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="891b1-149">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="891b1-149">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="891b1-150">Defina o valor para incrementar o contador em 10.</span><span class="sxs-lookup"><span data-stu-id="891b1-150">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="891b1-151">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="891b1-151">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="891b1-152">Recarregue o componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="891b1-152">Reload the `Index` component.</span></span> <span data-ttu-id="891b1-153">O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="891b1-153">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="891b1-154">O contador no componente `Counter` continua a incrementar em um.</span><span class="sxs-lookup"><span data-stu-id="891b1-154">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="891b1-155">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="891b1-155">Route to components</span></span>

<span data-ttu-id="891b1-156">A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente `Counter` é um ponto de extremidade de roteamento.</span><span class="sxs-lookup"><span data-stu-id="891b1-156">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="891b1-157">O componente `Counter` manipula solicitações enviadas para `/counter`.</span><span class="sxs-lookup"><span data-stu-id="891b1-157">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="891b1-158">Sem a diretiva `@page`, o componente não lidará com as solicitações roteadas, mas ele ainda poderá ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="891b1-158">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="891b1-159">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="891b1-159">Dependency injection</span></span>

<span data-ttu-id="891b1-160">Os serviços registrados no contêiner do serviço de aplicativo estão disponíveis para componentes por meio da [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="891b1-160">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="891b1-161">Injete os serviços em um componente usando a diretiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="891b1-161">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="891b1-162">Examine as diretivas do componente `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="891b1-162">Examine the directives of the `FetchData` component.</span></span>

<span data-ttu-id="891b1-163">Se estiver trabalhando com um aplicativo Blazor do lado do servidor, o serviço `WeatherForecastService` é registrado como um [singleton](xref:fundamentals/dependency-injection#service-lifetimes) e, portanto, uma instância do serviço estará disponível em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-163">If working with a Blazor server-side app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="891b1-164">A diretiva `@inject` é usada para injetar a instância do serviço `WeatherForecastService` no componente.</span><span class="sxs-lookup"><span data-stu-id="891b1-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="891b1-165">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="891b1-165">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="891b1-166">O componente `FetchData` usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="891b1-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="891b1-167">Se estiver trabalhando com um aplicativo Blazor do lado do cliente, `HttpClient` é injetado para obter dados sobre a previsão do tempo, do arquivo *weather.json*, na pasta *wwwroot/sample-data*:</span><span class="sxs-lookup"><span data-stu-id="891b1-167">If working with a Blazor client-side app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="891b1-168">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="891b1-168">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="891b1-169">Um loop [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) é usado para renderizar cada uma das instâncias de previsão do tempo como uma linha na tabela de dados do tempo:</span><span class="sxs-lookup"><span data-stu-id="891b1-169">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a><span data-ttu-id="891b1-170">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="891b1-170">Build a todo list</span></span>

<span data-ttu-id="891b1-171">Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="891b1-171">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="891b1-172">Adicione um arquivo vazio chamado *Todo.razor* ao aplicativo na pasta *Pages*:</span><span class="sxs-lookup"><span data-stu-id="891b1-172">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="891b1-173">Forneça a marcação inicial do componente:</span><span class="sxs-lookup"><span data-stu-id="891b1-173">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="891b1-174">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="891b1-174">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="891b1-175">O componente `NavMenu` (*Shared/NavMenu.razor*) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-175">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="891b1-176">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-176">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="891b1-177">Adicione um elemento `<NavLink>` para o componente `Todo` adicionando a seguinte marcação de item da lista abaixo dos itens existentes no arquivo *Shared/NavMenu.razor*:</span><span class="sxs-lookup"><span data-stu-id="891b1-177">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="891b1-178">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-178">Rebuild and run the app.</span></span> <span data-ttu-id="891b1-179">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.</span><span class="sxs-lookup"><span data-stu-id="891b1-179">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="891b1-180">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="891b1-180">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="891b1-181">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="891b1-181">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="891b1-182">Retorne para o componente `Todo` (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="891b1-182">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="891b1-183">Adicione um campo para os itens de tarefas pendentes em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="891b1-183">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="891b1-184">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="891b1-184">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="891b1-185">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="891b1-185">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="891b1-186">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="891b1-186">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="891b1-187">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="891b1-187">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="891b1-188">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-188">Rebuild and run the app.</span></span> <span data-ttu-id="891b1-189">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="891b1-189">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="891b1-190">Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="891b1-190">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="891b1-191">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="891b1-191">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="891b1-192">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="891b1-192">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="891b1-193">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="891b1-193">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="891b1-194">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="891b1-194">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="891b1-195">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-195">Rebuild and run the app.</span></span> <span data-ttu-id="891b1-196">Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="891b1-196">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="891b1-197">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="891b1-197">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="891b1-198">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="891b1-198">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="891b1-199">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="891b1-199">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="891b1-200">Para verificar se esses valores estão associados, atualize o cabeçalho `<h1>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="891b1-200">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="891b1-201">O componente `Todo` concluído (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="891b1-201">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="891b1-202">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="891b1-202">Rebuild and run the app.</span></span> <span data-ttu-id="891b1-203">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="891b1-203">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
