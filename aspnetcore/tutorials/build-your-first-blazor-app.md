---
title: Crie seu primeiro aplicativo Blazor
author: guardrex
description: Crie um aplicativo Blazor passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 8830dcf26b58b5f5fdd36b60298e7b365f99bdd9
ms.sourcegitcommit: 925cdbd94613243f33bc7613a62ea34006219931
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921286"
---
# <a name="build-your-first-opno-locblazor-app"></a><span data-ttu-id="29047-103">Crie seu primeiro aplicativo Blazor</span><span class="sxs-lookup"><span data-stu-id="29047-103">Build your first Blazor app</span></span>

<span data-ttu-id="29047-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29047-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="29047-105">Este tutorial mostra como criar e modificar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="29047-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="29047-106">Siga as orientações no artigo <xref:blazor/get-started> para criar um projeto Blazor para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="29047-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="29047-107">Nomeie o projeto como *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="29047-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="29047-108">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="29047-108">Build components</span></span>

1. <span data-ttu-id="29047-109">Navegue até cada uma das três páginas do aplicativo na pasta *páginas* : Home, Counter e FETCH Data.</span><span class="sxs-lookup"><span data-stu-id="29047-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="29047-110">Essas páginas são implementadas pelos arquivos de componente Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="29047-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="29047-111">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="29047-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="29047-112">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="29047-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="29047-113">Com Blazor, você pode escrever C# em vez disso.</span><span class="sxs-lookup"><span data-stu-id="29047-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="29047-114">Examine a implementação do componente `Counter` no arquivo *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="29047-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="29047-115">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="29047-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="29047-116">A interface do usuário do componente `Counter` é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="29047-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="29047-117">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="29047-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="29047-118">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="29047-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="29047-119">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="29047-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="29047-120">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="29047-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="29047-121">No bloco `@code`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="29047-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="29047-122">Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="29047-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="29047-123">Quando o botão **Clique aqui** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="29047-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="29047-124">O manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="29047-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="29047-125">O componente `Counter` regenera sua árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="29047-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="29047-126">A nova árvore de renderização é comparada à anterior.</span><span class="sxs-lookup"><span data-stu-id="29047-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="29047-127">Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="29047-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="29047-128">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="29047-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="29047-129">Modifique a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="29047-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="29047-130">Recompile e execute o aplicativo para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="29047-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="29047-131">Selecione o botão **Clique aqui**.</span><span class="sxs-lookup"><span data-stu-id="29047-131">Select the **Click me** button.</span></span> <span data-ttu-id="29047-132">O contador incrementa em dois.</span><span class="sxs-lookup"><span data-stu-id="29047-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="29047-133">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="29047-133">Use components</span></span>

<span data-ttu-id="29047-134">Inclua um componente em outro componente usando uma sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="29047-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="29047-135">Adicione o componente `Counter` no componente `Index` do aplicativo adicionando um elemento `<Counter />` ao componente `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="29047-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="29047-136">Se você estiver usando Blazor Webassembly para essa experiência, um componente de `SurveyPrompt` será usado pelo componente de `Index`.</span><span class="sxs-lookup"><span data-stu-id="29047-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="29047-137">Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="29047-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="29047-138">Se você estiver usando um aplicativo do Blazor Server para essa experiência, adicione o elemento `<Counter />` ao componente `Index`:</span><span class="sxs-lookup"><span data-stu-id="29047-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="29047-139">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="29047-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="29047-140">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29047-140">Rebuild and run the app.</span></span> <span data-ttu-id="29047-141">O componente `Index` tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="29047-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="29047-142">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="29047-142">Component parameters</span></span>

<span data-ttu-id="29047-143">Componentes também podem ter parâmetros.</span><span class="sxs-lookup"><span data-stu-id="29047-143">Components can also have parameters.</span></span> <span data-ttu-id="29047-144">Os parâmetros de componente são definidos usando propriedades públicas na classe Component com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="29047-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="29047-145">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="29047-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="29047-146">Atualize o código de `@code` C# do componente da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="29047-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="29047-147">Adicione uma propriedade de `IncrementAmount` pública com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="29047-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="29047-148">Altere o método `IncrementCount` para usar a propriedade `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="29047-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="29047-149">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="29047-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="29047-150">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="29047-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="29047-151">Defina o valor para incrementar o contador em 10.</span><span class="sxs-lookup"><span data-stu-id="29047-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="29047-152">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="29047-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="29047-153">Recarregue o componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="29047-153">Reload the `Index` component.</span></span> <span data-ttu-id="29047-154">O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="29047-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="29047-155">O contador no componente `Counter` continua a incrementar em um.</span><span class="sxs-lookup"><span data-stu-id="29047-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="29047-156">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="29047-156">Route to components</span></span>

<span data-ttu-id="29047-157">A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente `Counter` é um ponto de extremidade de roteamento.</span><span class="sxs-lookup"><span data-stu-id="29047-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="29047-158">O componente `Counter` manipula solicitações enviadas para `/counter`.</span><span class="sxs-lookup"><span data-stu-id="29047-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="29047-159">Sem a diretiva `@page`, o componente não lidará com as solicitações roteadas, mas ele ainda poderá ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="29047-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="29047-160">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="29047-160">Dependency injection</span></span>

### <a name="opno-locblazor-server-experience"></a><span data-ttu-id="29047-161">experiência do Blazor Server</span><span class="sxs-lookup"><span data-stu-id="29047-161">Blazor Server experience</span></span>

<span data-ttu-id="29047-162">Se estiver trabalhando com um aplicativo do Blazor Server, o serviço `WeatherForecastService` será registrado como um [singleton](xref:fundamentals/dependency-injection#service-lifetimes) no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="29047-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="29047-163">Uma instância do serviço está disponível em todo o aplicativo por meio de [injeção de dependência (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="29047-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="29047-164">A diretiva `@inject` é usada para injetar a instância do serviço `WeatherForecastService` no componente `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="29047-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="29047-165">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="29047-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="29047-166">O componente `FetchData` usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="29047-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a><span data-ttu-id="29047-167">experiência de Webassembly Blazor</span><span class="sxs-lookup"><span data-stu-id="29047-167">Blazor WebAssembly experience</span></span>

<span data-ttu-id="29047-168">Se estiver trabalhando com um aplicativo Webassembly Blazor, `HttpClient` será injetado para obter dados de previsão do tempo do arquivo *Weather. JSON* na pasta *wwwroot/Sample-data* .</span><span class="sxs-lookup"><span data-stu-id="29047-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="29047-169">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="29047-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="29047-170">Um loop de [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) é usado para renderizar cada instância de previsão como uma linha na tabela de dados meteorológicos:</span><span class="sxs-lookup"><span data-stu-id="29047-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="29047-171">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="29047-171">Build a todo list</span></span>

<span data-ttu-id="29047-172">Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="29047-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="29047-173">Adicione um arquivo vazio chamado *Todo.razor* ao aplicativo na pasta *Pages*:</span><span class="sxs-lookup"><span data-stu-id="29047-173">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="29047-174">Forneça a marcação inicial do componente:</span><span class="sxs-lookup"><span data-stu-id="29047-174">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="29047-175">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="29047-175">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="29047-176">O componente `NavMenu` (*Shared/NavMenu.razor*) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29047-176">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="29047-177">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29047-177">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="29047-178">Adicione um elemento `<NavLink>` para o componente `Todo` adicionando a seguinte marcação de item da lista abaixo dos itens existentes no arquivo *Shared/NavMenu.razor*:</span><span class="sxs-lookup"><span data-stu-id="29047-178">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="29047-179">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29047-179">Rebuild and run the app.</span></span> <span data-ttu-id="29047-180">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.</span><span class="sxs-lookup"><span data-stu-id="29047-180">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="29047-181">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="29047-181">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="29047-182">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="29047-182">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="29047-183">Retorne para o componente `Todo` (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="29047-183">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="29047-184">Adicione um campo para os itens de tarefas pendentes em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="29047-184">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="29047-185">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="29047-185">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="29047-186">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="29047-186">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="29047-187">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="29047-187">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="29047-188">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="29047-188">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="29047-189">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29047-189">Rebuild and run the app.</span></span> <span data-ttu-id="29047-190">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="29047-190">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="29047-191">Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="29047-191">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="29047-192">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="29047-192">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="29047-193">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="29047-193">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="29047-194">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="29047-194">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="29047-195">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="29047-195">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="29047-196">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29047-196">Rebuild and run the app.</span></span> <span data-ttu-id="29047-197">Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="29047-197">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="29047-198">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="29047-198">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="29047-199">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="29047-199">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="29047-200">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="29047-200">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="29047-201">Para verificar se esses valores estão associados, atualize o cabeçalho `<h1>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="29047-201">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="29047-202">O componente `Todo` concluído (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="29047-202">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="29047-203">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29047-203">Rebuild and run the app.</span></span> <span data-ttu-id="29047-204">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="29047-204">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
