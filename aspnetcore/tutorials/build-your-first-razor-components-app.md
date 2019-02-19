---
title: Compilar seu primeiro aplicativo com Razor Components
author: guardrex
description: Compile passo a passo um aplicativo com Razor Components e aprenda os conceitos básicos do Razor Components.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 0c3dd2366581d73bad44e2911602e13c6c0daf9a
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159337"
---
# <a name="build-your-first-razor-components-app"></a><span data-ttu-id="d0060-103">Compilar seu primeiro aplicativo com Razor Components</span><span class="sxs-lookup"><span data-stu-id="d0060-103">Build your first Razor Components app</span></span>

<span data-ttu-id="d0060-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0060-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="d0060-105">Este tutorial mostra como compilar um aplicativo com Razor Components e demonstra os conceitos básicos do Razor Components.</span><span class="sxs-lookup"><span data-stu-id="d0060-105">This tutorial shows you how to build an app with Razor Components and demonstrates basic Razor Components concepts.</span></span> <span data-ttu-id="d0060-106">Aproveite melhor esse tutorial usando um projeto com base em Razor Components (com suporte no .NET Core 3.0 ou posterior) ou usando um projeto com base em Blazor (com suporte em uma versão futura do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="d0060-106">You can enjoy this tutorial using either a Razor Components-based project (supported in .NET Core 3.0 or later) or using a Blazor-based project (supported in a future release of .NET Core).</span></span>

<span data-ttu-id="d0060-107">Para ter uma experiência de uso do Razor Components no ASP.NET Core (*recomendado*):</span><span class="sxs-lookup"><span data-stu-id="d0060-107">For an experience using ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="d0060-108">Siga as orientações em <xref:razor-components/get-started> para criar um projeto com base no Razor Components.</span><span class="sxs-lookup"><span data-stu-id="d0060-108">Follow the guidance in <xref:razor-components/get-started> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="d0060-109">Nomeie o projeto `RazorComponents`.</span><span class="sxs-lookup"><span data-stu-id="d0060-109">Name the project `RazorComponents`.</span></span>
* <span data-ttu-id="d0060-110">Uma solução com vários projetos é criada do modelo Razor Components.</span><span class="sxs-lookup"><span data-stu-id="d0060-110">A multi-project solution is created from the Razor Components template.</span></span> <span data-ttu-id="d0060-111">O projeto em Razor Components é gerado como *RazorComponents.App*.</span><span class="sxs-lookup"><span data-stu-id="d0060-111">The Razor Components project is generated as *RazorComponents.App*.</span></span>

<span data-ttu-id="d0060-112">Para ter uma experiência de uso do Blazor:</span><span class="sxs-lookup"><span data-stu-id="d0060-112">For an experience using Blazor:</span></span>

* <span data-ttu-id="d0060-113">Siga as orientações em <xref:spa/blazor/get-started> para criar um projeto com base no Blazor.</span><span class="sxs-lookup"><span data-stu-id="d0060-113">Follow the guidance in <xref:spa/blazor/get-started> to create a Blazor-based project.</span></span>
* <span data-ttu-id="d0060-114">Nomeie o projeto `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="d0060-114">Name the project `Blazor`.</span></span>
* <span data-ttu-id="d0060-115">Uma solução de projeto único é criada do modelo de Blazor.</span><span class="sxs-lookup"><span data-stu-id="d0060-115">A single-project solution is created from the Blazor template.</span></span>

<span data-ttu-id="d0060-116">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d0060-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="d0060-117">Conheça os pré-requisitos nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d0060-117">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="d0060-118">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="d0060-118">Build components</span></span>

1. <span data-ttu-id="d0060-119">Navegue para cada uma das três páginas do aplicativo: Início, Contador e Buscar dados.</span><span class="sxs-lookup"><span data-stu-id="d0060-119">Browse to each of the app's three pages: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="d0060-120">Essas páginas são implementadas por arquivos Razor na pasta *Páginas*: *Index.cshtml*, *Counter.cshtml* e *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0060-120">These pages are implemented by Razor files in the *Pages* folder: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.</span></span>

1. <span data-ttu-id="d0060-121">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="d0060-121">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="d0060-122">A incrementação de um contador em uma página da Web normalmente exige JavaScript, mas o Razor Components fornece uma abordagem melhor usando C#.</span><span class="sxs-lookup"><span data-stu-id="d0060-122">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

1. <span data-ttu-id="d0060-123">Examine a implementação do componente Counter no arquivo *Counter.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0060-123">Examine the implementation of the Counter component in the *Counter.cshtml* file.</span></span>

   <span data-ttu-id="d0060-124">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d0060-124">*Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.cshtml)]

   <span data-ttu-id="d0060-125">A interface do usuário do componente Counter é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="d0060-125">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="d0060-126">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="d0060-126">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="d0060-127">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="d0060-127">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="d0060-128">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="d0060-128">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="d0060-129">Os membros da classe de componente são definidos em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="d0060-129">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="d0060-130">No bloco `@functions`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="d0060-130">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="d0060-131">Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="d0060-131">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="d0060-132">Quando o botão **Clique aqui** botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="d0060-132">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="d0060-133">O manipulador `onclick` registrado do componente Counter é chamado (o método `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="d0060-133">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="d0060-134">O componente Counter regenera sua árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="d0060-134">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="d0060-135">A nova árvore de renderização é comparada à anterior.</span><span class="sxs-lookup"><span data-stu-id="d0060-135">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="d0060-136">Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="d0060-136">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="d0060-137">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="d0060-137">The displayed count is updated.</span></span>

1. <span data-ttu-id="d0060-138">Modifique a lógica de C# do componente Counter para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="d0060-138">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.cshtml?highlight=14)]

1. <span data-ttu-id="d0060-139">Recompile e execute o aplicativo para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="d0060-139">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="d0060-140">Selecione o botão **Clique aqui**, e o contador é incrementado em dois.</span><span class="sxs-lookup"><span data-stu-id="d0060-140">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="d0060-141">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="d0060-141">Use components</span></span>

<span data-ttu-id="d0060-142">Inclua um componente em outro componente usando uma sintaxe semelhante a HTML.</span><span class="sxs-lookup"><span data-stu-id="d0060-142">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="d0060-143">Adicione o componente Counter ao componente Index (página inicial) do aplicativo adicionando um elemento `<Counter />` ao componente Index.</span><span class="sxs-lookup"><span data-stu-id="d0060-143">Add the Counter component to the app's Index (home page) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="d0060-144">Se você estiver usando Blazor para essa experiência, um componente Survey Prompt (elemento `<SurveyPrompt>`) estará no componente Index.</span><span class="sxs-lookup"><span data-stu-id="d0060-144">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="d0060-145">Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter>`.</span><span class="sxs-lookup"><span data-stu-id="d0060-145">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="d0060-146">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d0060-146">*Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.cshtml?highlight=7)]

1. <span data-ttu-id="d0060-147">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-147">Rebuild and run the app.</span></span> <span data-ttu-id="d0060-148">A home page tem seu próprio contador.</span><span class="sxs-lookup"><span data-stu-id="d0060-148">The home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="d0060-149">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="d0060-149">Component parameters</span></span>

<span data-ttu-id="d0060-150">Componentes também podem ter parâmetros.</span><span class="sxs-lookup"><span data-stu-id="d0060-150">Components can also have parameters.</span></span> <span data-ttu-id="d0060-151">Os parâmetros do componente são definidos usando propriedades não públicas na classe de componente decorada com `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="d0060-151">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="d0060-152">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="d0060-152">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="d0060-153">Atualizar o código C# do componente `@functions`:</span><span class="sxs-lookup"><span data-stu-id="d0060-153">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="d0060-154">Adicione uma propriedade `IncrementAmount` decorada com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="d0060-154">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="d0060-155">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="d0060-155">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="d0060-156">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d0060-156">*Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Counter.cshtml?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="d0060-157">Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente Home usando um atributo.</span><span class="sxs-lookup"><span data-stu-id="d0060-157">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="d0060-158">Defina o valor para incrementar o contador em 10.</span><span class="sxs-lookup"><span data-stu-id="d0060-158">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="d0060-159">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d0060-159">*Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Index.cshtml?highlight=7)]

1. <span data-ttu-id="d0060-160">Recarregue a página.</span><span class="sxs-lookup"><span data-stu-id="d0060-160">Reload the page.</span></span> <span data-ttu-id="d0060-161">O contador de página inicial é incrementado em 10 sempre que o botão **Clique aqui** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="d0060-161">The home page counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="d0060-162">O contador na página *Contador* aumenta em um.</span><span class="sxs-lookup"><span data-stu-id="d0060-162">The counter on the *Counter* page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="d0060-163">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="d0060-163">Route to components</span></span>

<span data-ttu-id="d0060-164">A diretiva `@page` no início do arquivo *Counter.cshtml* especifica que esse componente é um ponto de extremidade de roteamento.</span><span class="sxs-lookup"><span data-stu-id="d0060-164">The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="d0060-165">O componente Counter manipula solicitações enviadas para `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="d0060-165">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="d0060-166">Sem a diretiva `@page`, o componente não trata as solicitações roteadas, mas o componente ainda pode ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="d0060-166">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="d0060-167">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d0060-167">Dependency injection</span></span>

<span data-ttu-id="d0060-168">Os serviços registrados no contêiner do serviço de aplicativo estão disponíveis para componentes por meio da [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d0060-168">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d0060-169">Injete os serviços em um componente usando a diretiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="d0060-169">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="d0060-170">Examine as diretivas do componente FetchData (*Pages/FetchData.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="d0060-170">Examine the directives of the FetchData component (*Pages/FetchData.cshtml*).</span></span> <span data-ttu-id="d0060-171">A diretiva `@inject` é usada para injetar a instância do serviço `WeatherForecastService` no componente:</span><span class="sxs-lookup"><span data-stu-id="d0060-171">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=3)]

<span data-ttu-id="d0060-172">O serviço `WeatherForecastService` é registrado como um [singleton](xref:fundamentals/dependency-injection#service-lifetimes), portanto, uma instância do serviço está disponível em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-172">The `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span>

<span data-ttu-id="d0060-173">O componente FetchData usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:</span><span class="sxs-lookup"><span data-stu-id="d0060-173">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.cshtml?highlight=6)]

<span data-ttu-id="d0060-174">Um loop [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) é usado para renderizar cada instância de previsão como uma linha na tabela de dados meteorológicos:</span><span class="sxs-lookup"><span data-stu-id="d0060-174">A [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.cshtml?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="d0060-175">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="d0060-175">Build a todo list</span></span>

<span data-ttu-id="d0060-176">Adicione uma nova página ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="d0060-176">Add a new page to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="d0060-177">Adicione um arquivo vazio à pasta *Páginas* denominado *Todo.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d0060-177">Add an empty file to the *Pages* folder named *Todo.cshtml*.</span></span>

1. <span data-ttu-id="d0060-178">Forneça a marcação inicial à página:</span><span class="sxs-lookup"><span data-stu-id="d0060-178">Provide the initial markup for the page:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="d0060-179">Adicione a página Tarefas Pendentes à barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="d0060-179">Add the Todo page to the navigation bar.</span></span>

   <span data-ttu-id="d0060-180">O componente NavMenu (*Shared/NavMenu.csthml*) é usado no layout do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-180">The NavMenu component (*Shared/NavMenu.csthml*) is used in the app's layout.</span></span> <span data-ttu-id="d0060-181">Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-181">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="d0060-182">Para obter mais informações, consulte <xref:razor-components/layouts>.</span><span class="sxs-lookup"><span data-stu-id="d0060-182">For more information, see <xref:razor-components/layouts>.</span></span>

   <span data-ttu-id="d0060-183">Adicione um `<NavLink>` à página de Tarefas Pendentes, incluindo a marcação de item de lista a seguir, abaixo dos itens de lista existentes no arquivo *Shared/NavMenu.csthml*:</span><span class="sxs-lookup"><span data-stu-id="d0060-183">Add a `<NavLink>` for the Todo page by adding the following list item markup below the existing list items in the *Shared/NavMenu.csthml* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="d0060-184">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-184">Rebuild and run the app.</span></span> <span data-ttu-id="d0060-185">Visite a nova página de Tarefas Pendentes para confirmar se o link para a página está funcionando.</span><span class="sxs-lookup"><span data-stu-id="d0060-185">Visit the new Todo page to confirm that the link to the Todo page works.</span></span>

1. <span data-ttu-id="d0060-186">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="d0060-186">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="d0060-187">Use o seguinte código C# para a classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="d0060-187">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/TodoItem.cs)]

1. <span data-ttu-id="d0060-188">Retorne ao componente Todo (*Todo.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="d0060-188">Return to the Todo component (*Todo.cshtml*):</span></span>

   * <span data-ttu-id="d0060-189">Adicione um campo para as tarefas pendentes em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="d0060-189">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="d0060-190">O componente Todo usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="d0060-190">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="d0060-191">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista.</span><span class="sxs-lookup"><span data-stu-id="d0060-191">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData4.cshtml?highlight=5-10,12-14)]

1. <span data-ttu-id="d0060-192">O aplicativo requer os elementos de interface do usuário para adicionar tarefas pendentes à lista.</span><span class="sxs-lookup"><span data-stu-id="d0060-192">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="d0060-193">Adicione uma entrada de texto e um botão abaixo da lista:</span><span class="sxs-lookup"><span data-stu-id="d0060-193">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData5.cshtml?highlight=12-13)]

1. <span data-ttu-id="d0060-194">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-194">Rebuild and run the app.</span></span> <span data-ttu-id="d0060-195">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="d0060-195">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="d0060-196">Adicione um método `AddTodo` ao componente Todo e registre-o para cliques de botão usando o atributo `onclick`:</span><span class="sxs-lookup"><span data-stu-id="d0060-196">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData6.cshtml?highlight=2,7-10)]

   <span data-ttu-id="d0060-197">O método `AddTodo` em C# é chamado quando o botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="d0060-197">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="d0060-198">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` e associe-o ao valor da próxima entrada de texto usando o atributo `bind`:</span><span class="sxs-lookup"><span data-stu-id="d0060-198">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData7.cshtml?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. <span data-ttu-id="d0060-199">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="d0060-199">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="d0060-200">Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="d0060-200">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData8.cshtml?highlight=19-26)]

1. <span data-ttu-id="d0060-201">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-201">Rebuild and run the app.</span></span> <span data-ttu-id="d0060-202">Adicione algumas tarefas à lista de tarefas para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="d0060-202">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="d0060-203">O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos.</span><span class="sxs-lookup"><span data-stu-id="d0060-203">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="d0060-204">Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="d0060-204">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="d0060-205">Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="d0060-205">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData9.cshtml?highlight=5-6)]

1. <span data-ttu-id="d0060-206">Para verificar se esses valores estão associados, atualize o cabeçalho `<h1>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="d0060-206">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="d0060-207">O componente Todo concluído (*Todo.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="d0060-207">The completed Todo component (*Todo.cshtml*):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Todo.cshtml)]

1. <span data-ttu-id="d0060-208">Recompile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d0060-208">Rebuild and run the app.</span></span> <span data-ttu-id="d0060-209">Adicione itens de tarefa pendente para testar o novo código.</span><span class="sxs-lookup"><span data-stu-id="d0060-209">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="d0060-210">Publicar e implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d0060-210">Publish and deploy the app</span></span>

<span data-ttu-id="d0060-211">Para publicar o aplicativo, confira <xref:host-and-deploy/razor-components/index#publish-the-app>.</span><span class="sxs-lookup"><span data-stu-id="d0060-211">To publish the app, see <xref:host-and-deploy/razor-components/index#publish-the-app>.</span></span>
