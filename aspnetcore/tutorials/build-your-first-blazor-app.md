---
title: Como criar seu primeiro aplicativo Blazor
author: guardrex
description: Crie um aplicativo Blazor passo a passo.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: b433d793ae615bc4ece7c63bebd72d349adf43ee
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081259"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="6923a-103">Como criar seu primeiro aplicativo Blazor</span><span class="sxs-lookup"><span data-stu-id="6923a-103">Build your first Blazor app</span></span>

<span data-ttu-id="6923a-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6923a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6923a-105">Este tutorial mostra como criar e modificar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="6923a-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="6923a-106">Siga as orientações no artigo <xref:blazor/get-started> para criar um projeto Blazor para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="6923a-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="6923a-107">Nomeie o projeto como *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="6923a-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="6923a-108">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="6923a-108">Build components</span></span>

1. <span data-ttu-id="6923a-109">Navegue até cada uma das três páginas do aplicativo na pasta *Pages*: Início, Contador e Buscar dados.</span><span class="sxs-lookup"><span data-stu-id="6923a-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="6923a-110">Essas páginas são implementadas pelos arquivos de componente Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="6923a-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="6923a-111">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="6923a-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="6923a-112">A incrementação de um contador em uma página da Web normalmente exige JavaScript, mas o Blazor fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="6923a-112">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="6923a-113">Examine a implementação do componente `Counter` no arquivo *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="6923a-113">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="6923a-114">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6923a-114">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="6923a-115">A interface do usuário do componente `Counter` é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="6923a-115">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="6923a-116">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="6923a-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="6923a-117">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="6923a-117">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="6923a-118">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="6923a-118">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="6923a-119">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="6923a-119">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="6923a-120">No bloco `@code`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="6923a-120">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="6923a-121">Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="6923a-121">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="6923a-122">Quando o botão **Clique aqui** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="6923a-122">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="6923a-123">O manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="6923a-123">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="6923a-124">O componente `Counter` regenera sua árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="6923a-124">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="6923a-125">A nova árvore de renderização é comparada à anterior.</span><span class="sxs-lookup"><span data-stu-id="6923a-125">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="6923a-126">Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="6923a-126">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="6923a-127">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="6923a-127">The displayed count is updated.</span></span>

1. <span data-ttu-id="6923a-128">Modifique a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="6923a-128">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="6923a-129">Recompile e execute o aplicativo para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="6923a-129">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="6923a-130">Selecione o botão **Clique aqui**.</span><span class="sxs-lookup"><span data-stu-id="6923a-130">Select the **Click me** button.</span></span> <span data-ttu-id="6923a-131">O contador incrementa em dois.</span><span class="sxs-lookup"><span data-stu-id="6923a-131">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="6923a-132">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="6923a-132">Use components</span></span>

<span data-ttu-id="6923a-133">Inclua um componente em outro componente usando uma sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="6923a-133">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="6923a-134">Adicione o componente `Counter` no componente `Index` do aplicativo adicionando um elemento `<Counter />` ao componente `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="6923a-134">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="6923a-135">Se você estiver usando Webassembly mais experiente para essa experiência, um `SurveyPrompt` componente será usado `Index` pelo componente.</span><span class="sxs-lookup"><span data-stu-id="6923a-135">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="6923a-136">Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="6923a-136">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="6923a-137">Se você estiver usando um aplicativo de servidor mais experiente para essa experiência, adicione `<Counter />` o elemento `Index` ao componente:</span><span class="sxs-lookup"><span data-stu-id="6923a-137">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="6923a-138">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6923a-138">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="6923a-139">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6923a-139">Rebuild and run the app.</span></span> <span data-ttu-id="6923a-140">O componente `Index` tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="6923a-140">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="6923a-141">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="6923a-141">Component parameters</span></span>

<span data-ttu-id="6923a-142">Componentes também podem ter parâmetros.</span><span class="sxs-lookup"><span data-stu-id="6923a-142">Components can also have parameters.</span></span> <span data-ttu-id="6923a-143">Os parâmetros de componente são definidos usando propriedades públicas na classe de componente `[Parameter]` com o atributo.</span><span class="sxs-lookup"><span data-stu-id="6923a-143">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="6923a-144">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="6923a-144">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="6923a-145">Atualizar o código C# do componente `@code`:</span><span class="sxs-lookup"><span data-stu-id="6923a-145">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="6923a-146">Adicione uma propriedade `IncrementAmount` pública com o `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6923a-146">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="6923a-147">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="6923a-147">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="6923a-148">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6923a-148">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="6923a-149">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="6923a-149">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="6923a-150">Defina o valor para incrementar o contador em 10.</span><span class="sxs-lookup"><span data-stu-id="6923a-150">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="6923a-151">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6923a-151">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="6923a-152">Recarregue o componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="6923a-152">Reload the `Index` component.</span></span> <span data-ttu-id="6923a-153">O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6923a-153">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="6923a-154">O contador no componente `Counter` continua a incrementar em um.</span><span class="sxs-lookup"><span data-stu-id="6923a-154">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="6923a-155">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="6923a-155">Route to components</span></span>

<span data-ttu-id="6923a-156">A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente `Counter` é um ponto de extremidade de roteamento.</span><span class="sxs-lookup"><span data-stu-id="6923a-156">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="6923a-157">O componente `Counter` manipula solicitações enviadas para `/counter`.</span><span class="sxs-lookup"><span data-stu-id="6923a-157">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="6923a-158">Sem a diretiva `@page`, o componente não lidará com as solicitações roteadas, mas ele ainda poderá ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="6923a-158">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="6923a-159">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6923a-159">Dependency injection</span></span>

<span data-ttu-id="6923a-160">Se estiver trabalhando com um aplicativo de servidor mais incrivelmente `WeatherForecastService` , o serviço será registrado como um `Startup.ConfigureServices` [singleton](xref:fundamentals/dependency-injection#service-lifetimes) no.</span><span class="sxs-lookup"><span data-stu-id="6923a-160">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6923a-161">Uma instância do serviço está disponível em todo o aplicativo por meio de [injeção de dependência (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="6923a-161">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="6923a-162">A `@inject` diretiva é usada para injetar a instância `WeatherForecastService` do serviço no `FetchData` componente.</span><span class="sxs-lookup"><span data-stu-id="6923a-162">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="6923a-163">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="6923a-163">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="6923a-164">O componente `FetchData` usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="6923a-164">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="6923a-165">Se estiver trabalhando com um aplicativo Webassembly de mais `HttpClient` alto, o é injetado para obter dados de previsão do tempo do arquivo *Weather. JSON* na pasta *wwwroot/Sample-data* .</span><span class="sxs-lookup"><span data-stu-id="6923a-165">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="6923a-166">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="6923a-166">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="6923a-167">Um loop [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) é usado para renderizar cada uma das instâncias de previsão do tempo como uma linha na tabela de dados do tempo:</span><span class="sxs-lookup"><span data-stu-id="6923a-167">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="6923a-168">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6923a-168">Build a todo list</span></span>

<span data-ttu-id="6923a-169">Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="6923a-169">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="6923a-170">Adicione um arquivo vazio chamado *Todo.razor* ao aplicativo na pasta *Pages*:</span><span class="sxs-lookup"><span data-stu-id="6923a-170">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="6923a-171">Forneça a marcação inicial do componente:</span><span class="sxs-lookup"><span data-stu-id="6923a-171">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="6923a-172">Adicione o componente `Todo` à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="6923a-172">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="6923a-173">O componente `NavMenu` (*Shared/NavMenu.razor*) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6923a-173">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="6923a-174">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6923a-174">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="6923a-175">Adicione um elemento `<NavLink>` para o componente `Todo` adicionando a seguinte marcação de item da lista abaixo dos itens existentes no arquivo *Shared/NavMenu.razor*:</span><span class="sxs-lookup"><span data-stu-id="6923a-175">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="6923a-176">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6923a-176">Rebuild and run the app.</span></span> <span data-ttu-id="6923a-177">Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.</span><span class="sxs-lookup"><span data-stu-id="6923a-177">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="6923a-178">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="6923a-178">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="6923a-179">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6923a-179">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="6923a-180">Retorne para o componente `Todo` (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="6923a-180">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="6923a-181">Adicione um campo para os itens de tarefas pendentes em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="6923a-181">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="6923a-182">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="6923a-182">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="6923a-183">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="6923a-183">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="6923a-184">O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista.</span><span class="sxs-lookup"><span data-stu-id="6923a-184">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="6923a-185">Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="6923a-185">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="6923a-186">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6923a-186">Rebuild and run the app.</span></span> <span data-ttu-id="6923a-187">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="6923a-187">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="6923a-188">Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="6923a-188">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="6923a-189">O método C# `AddTodo` é chamado quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="6923a-189">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="6923a-190">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="6923a-190">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="6923a-191">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="6923a-191">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="6923a-192">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="6923a-192">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="6923a-193">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6923a-193">Rebuild and run the app.</span></span> <span data-ttu-id="6923a-194">Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="6923a-194">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="6923a-195">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="6923a-195">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="6923a-196">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="6923a-196">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="6923a-197">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="6923a-197">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="6923a-198">Para verificar se esses valores estão associados, atualize o cabeçalho `<h1>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="6923a-198">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="6923a-199">O componente `Todo` concluído (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="6923a-199">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="6923a-200">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6923a-200">Rebuild and run the app.</span></span> <span data-ttu-id="6923a-201">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="6923a-201">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
