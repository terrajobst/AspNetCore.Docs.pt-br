---
title: Como criar seu primeiro aplicativo Blazor
author: guardrex
description: Crie um aplicativo Blazor passo a passo e aprenda rapidamente sobre os recursos básicos da estrutura do Razor Components.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 99396e200eb121524abccc20a7d461062c94ecc7
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667963"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="b0756-103">Como criar seu primeiro aplicativo Blazor</span><span class="sxs-lookup"><span data-stu-id="b0756-103">Build your first Blazor app</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="b0756-104">Neste tutorial, você cria um aplicativo Blazor passo a passo e aprende rapidamente sobre os recursos básicos da estrutura do Razor Components.</span><span class="sxs-lookup"><span data-stu-id="b0756-104">In this tutorial, you build a Blazor app step-by-step and quickly learn the basic features of the Razor Components framework.</span></span>

<span data-ttu-id="b0756-105">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b0756-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b0756-106">Veja o tópico [Introdução](xref:razor-components/get-started) para conhecer os pré-requisitos.</span><span class="sxs-lookup"><span data-stu-id="b0756-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="b0756-107">Para criar o projeto no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b0756-107">To create the project in Visual Studio:</span></span>

1. <span data-ttu-id="b0756-108">Selecione **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="b0756-108">Select **File** > **New** > **Project**.</span></span> <span data-ttu-id="b0756-109">Selecione **Web** > **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b0756-109">Select **Web** > **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b0756-110">Atribua o nome "BlazorApp1" ao projeto no campo **Nome**.</span><span class="sxs-lookup"><span data-stu-id="b0756-110">Name the project "BlazorApp1" in the **Name** field.</span></span> <span data-ttu-id="b0756-111">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="b0756-111">Select **OK**.</span></span>

    ![Novo projeto ASP.NET Core](build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. <span data-ttu-id="b0756-113">A caixa de diálogo **Novo Aplicativo Web ASP.NET Core** é exibida.</span><span class="sxs-lookup"><span data-stu-id="b0756-113">The **New ASP.NET Core Web Application** dialog appears.</span></span> <span data-ttu-id="b0756-114">Verifique se **.NET Core** está selecionado na parte superior.</span><span class="sxs-lookup"><span data-stu-id="b0756-114">Make sure **.NET Core** is selected at the top.</span></span> <span data-ttu-id="b0756-115">Selecione **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="b0756-115">Select **ASP.NET Core 2.1**.</span></span> <span data-ttu-id="b0756-116">Escolha o modelo **Blazor** e selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="b0756-116">Choose the **Blazor** template and select **OK**.</span></span>

    ![Caixa de diálogo Novo aplicativo Blazor](build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. <span data-ttu-id="b0756-118">Após a criação do projeto, pressione **Ctrl+F5** para executar o aplicativo *sem o depurador*.</span><span class="sxs-lookup"><span data-stu-id="b0756-118">Once the project is created, press **Ctrl-F5** to run the app *without the debugger*.</span></span> <span data-ttu-id="b0756-119">A execução com o depurador (**F5**) não tem suporte no momento.</span><span class="sxs-lookup"><span data-stu-id="b0756-119">Running with the debugger (**F5**) isn't supported at this time.</span></span>

> [!NOTE]
> <span data-ttu-id="b0756-120">Se não estiver usando o Visual Studio, crie o aplicativo Blazor em um prompt de comando no Windows, macOS ou Linux:</span><span class="sxs-lookup"><span data-stu-id="b0756-120">If not using Visual Studio, create the Blazor app at a command prompt on Windows, macOS, or Linux:</span></span>
>
> ```console
> dotnet new --install "Microsoft.AspNetCore.Blazor.Templates"
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> <span data-ttu-id="b0756-121">Navegue até o aplicativo usando o endereço do localhost e a porta fornecidos na saída da janela do console, após a execução de `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b0756-121">Navigate to the app using the localhost address and port provided in the console window output after `dotnet run` is executed.</span></span> <span data-ttu-id="b0756-122">Use **Ctrl+C** na janela do console para encerrar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0756-122">Use **Ctrl-C** in the console window to shutdown the app.</span></span>

<span data-ttu-id="b0756-123">O aplicativo Blazor é executado no navegador:</span><span class="sxs-lookup"><span data-stu-id="b0756-123">The Blazor app runs in the browser:</span></span>

![Home page do aplicativo Blazor](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## <a name="build-components"></a><span data-ttu-id="b0756-125">Componentes do build</span><span class="sxs-lookup"><span data-stu-id="b0756-125">Build components</span></span>

1. <span data-ttu-id="b0756-126">Navegue para cada uma das três páginas do aplicativo: Início, Contador e Buscar dados.</span><span class="sxs-lookup"><span data-stu-id="b0756-126">Browse to each of the app's three pages: Home, Counter, and Fetch data.</span></span>

    <span data-ttu-id="b0756-127">Essas três páginas são implementadas por três arquivos Razor na pasta *Páginas*: *Index.cshtml*, *Counter.cshtml* e *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b0756-127">These three pages are implemented by the three Razor files in the *Pages* folder: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.</span></span> <span data-ttu-id="b0756-128">Cada um desses arquivos implementa um componente que é compilado e executado pelo cliente no navegador.</span><span class="sxs-lookup"><span data-stu-id="b0756-128">Each of these files implements a component that's compiled and executed client-side in the browser.</span></span>

1. <span data-ttu-id="b0756-129">Selecione o botão na página Contador.</span><span class="sxs-lookup"><span data-stu-id="b0756-129">Select the button on the Counter page.</span></span>

    ![Home page do aplicativo Blazor](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    <span data-ttu-id="b0756-131">Toda vez que o botão é selecionado, o contador é incrementado sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="b0756-131">Each time the button is selected, the counter is incremented without a page refresh.</span></span> <span data-ttu-id="b0756-132">Normalmente, esse tipo de comportamento do cliente é tratado no JavaScript. Aqui, ele é implementado em C# e .NET pelo componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="b0756-132">Normally, this kind of client-side behavior is handled in JavaScript; but here, it's implemented in C# and .NET by the `Counter` component.</span></span>

1. <span data-ttu-id="b0756-133">Observe a implementação do componente `Counter` no arquivo *Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b0756-133">Take a look at the implementation of the `Counter` component in the *Counter.cshtml* file:</span></span>

    ```cshtml
    @page "/counter"

    <h1>Counter</h1>

    <p>Current count: @currentCount</p>

    <button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
    ```

    <span data-ttu-id="b0756-134">A interface do usuário do componente `Counter` é definida usando HTML normal.</span><span class="sxs-lookup"><span data-stu-id="b0756-134">The UI for the `Counter` component is defined using normal HTML.</span></span> <span data-ttu-id="b0756-135">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b0756-135">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span></span> <span data-ttu-id="b0756-136">A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="b0756-136">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="b0756-137">O nome da classe .NET gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="b0756-137">The name of the generated .NET class matches the name of the file.</span></span>

    <span data-ttu-id="b0756-138">Os membros da classe de componente são definidos em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="b0756-138">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="b0756-139">No bloco `@functions`, o estado do componente (propriedades, campos) e os métodos podem ser especificados para manipulação de eventos ou para definição de outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="b0756-139">In the `@functions` block, component state (properties, fields) and methods can be specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="b0756-140">Assim, esses membros podem ser usados como parte da lógica de renderização do componente e da manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="b0756-140">These members can then be used as part of the component's rendering logic and for handling events.</span></span>

    <span data-ttu-id="b0756-141">Quando o botão é selecionado, o manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`), e o componente `Counter` regenera a respectiva árvore de renderização.</span><span class="sxs-lookup"><span data-stu-id="b0756-141">When the button is selected, the `Counter` component's registered `onclick` handler is called (the `IncrementCount` method) and the `Counter` component regenerates its render tree.</span></span> <span data-ttu-id="b0756-142">O Blazor compara a nova árvore de renderização em relação à anterior e aplica todas as modificações ao DOM (Modelo de Objeto do Documento) do navegador.</span><span class="sxs-lookup"><span data-stu-id="b0756-142">Blazor compares the new render tree against the previous one and applies any modifications to the browser Document Object Model (DOM).</span></span> <span data-ttu-id="b0756-143">A contagem exibida é atualizada.</span><span class="sxs-lookup"><span data-stu-id="b0756-143">The displayed count is updated.</span></span>

1. <span data-ttu-id="b0756-144">Atualize a marcação para o componente `Counter` de modo a tornar o cabeçalho de nível superior mais *instigante*.</span><span class="sxs-lookup"><span data-stu-id="b0756-144">Update the markup for the `Counter` component to make the top-level header more *exciting*.</span></span>

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. <span data-ttu-id="b0756-145">Modifique também a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.</span><span class="sxs-lookup"><span data-stu-id="b0756-145">Also modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. <span data-ttu-id="b0756-146">Atualize a página do contador no navegador para ver as alterações.</span><span class="sxs-lookup"><span data-stu-id="b0756-146">Refresh the counter page in the browser to see the changes.</span></span>

    ![Contador instigante](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## <a name="use-components"></a><span data-ttu-id="b0756-148">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="b0756-148">Use components</span></span>

<span data-ttu-id="b0756-149">Depois de definir um componente, ele poderá ser usado para implementar outros componentes.</span><span class="sxs-lookup"><span data-stu-id="b0756-149">After a component is defined, the component can be used to implement other components.</span></span> <span data-ttu-id="b0756-150">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="b0756-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

1. <span data-ttu-id="b0756-151">Adicione um componente `Counter` à home page do aplicativo (*index.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="b0756-151">Add a `Counter` component to the Home page of the app (*Index.cshtml*).</span></span>

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. <span data-ttu-id="b0756-152">Atualize a home page no navegador.</span><span class="sxs-lookup"><span data-stu-id="b0756-152">Refresh the home page in the browser.</span></span> <span data-ttu-id="b0756-153">Observe a instância separada do componente `Counter` na home page.</span><span class="sxs-lookup"><span data-stu-id="b0756-153">Note the separate instance of the `Counter` component on the Home page.</span></span>

    ![Home page do Blazer com contador](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## <a name="component-parameters"></a><span data-ttu-id="b0756-155">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="b0756-155">Component parameters</span></span>

<span data-ttu-id="b0756-156">Os componentes também podem ter parâmetros que são definidos usando as propriedades privadas na classe de componente decorada com `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="b0756-156">Components can also have parameters, which are defined using private properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="b0756-157">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="b0756-157">Use attributes to specify arguments for a component in markup.</span></span> 

1. <span data-ttu-id="b0756-158">Atualize o componente `Counter` para ter um parâmetro `IncrementAmount` que seja padronizado como 1.</span><span class="sxs-lookup"><span data-stu-id="b0756-158">Update the `Counter` component to have an `IncrementAmount` parameter that defaults to 1.</span></span>

    ```cshtml
    @functions {
        int currentCount = 0;

        [Parameter]
        private int IncrementAmount { get; set; } = 1;

        void IncrementCount()
        {
            currentCount += IncrementAmount;
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="b0756-159">No Visual Studio, você pode adicionar um parâmetro de componente rapidamente usando o snippet de código `para`.</span><span class="sxs-lookup"><span data-stu-id="b0756-159">From Visual Studio, you can quickly add a component parameter by using the `para` snippet.</span></span> <span data-ttu-id="b0756-160">Digite `para` e pressione a tecla `Tab` duas vezes.</span><span class="sxs-lookup"><span data-stu-id="b0756-160">Type `para` and then press the `Tab` key twice.</span></span>

1. <span data-ttu-id="b0756-161">Na home page (*index.cshtml*), altere o valor do incremento do `Counter` para 10 configurando um atributo que corresponda ao nome da propriedade do componente de `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="b0756-161">On the Home page (*Index.cshtml*), change the increment amount for the `Counter` to 10 by setting an attribute that matches the name of the component's property for `IncrementCount`.</span></span>

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. <span data-ttu-id="b0756-162">Recarregue a página.</span><span class="sxs-lookup"><span data-stu-id="b0756-162">Reload the page.</span></span>

    <span data-ttu-id="b0756-163">O incremento do contador na home page agora é 10, enquanto o incremento do contador na página Contador ainda é 1.</span><span class="sxs-lookup"><span data-stu-id="b0756-163">The counter on the Home page now increments by 10, while the counter on the Counter page still increments by 1.</span></span>

    ![Contagem de Blazor por dez](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## <a name="route-to-components"></a><span data-ttu-id="b0756-165">Rotear para componentes</span><span class="sxs-lookup"><span data-stu-id="b0756-165">Route to components</span></span>

<span data-ttu-id="b0756-166">A diretiva `@page` no início do arquivo *Counter.cshtml* especifica que esse componente é uma página para a qual as solicitações podem ser roteadas.</span><span class="sxs-lookup"><span data-stu-id="b0756-166">The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a page to which requests can be routed.</span></span> <span data-ttu-id="b0756-167">Especificamente, o componente `Counter` manipula solicitações enviadas para `/Counter`.</span><span class="sxs-lookup"><span data-stu-id="b0756-167">Specifically, the `Counter` component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="b0756-168">Sem a diretiva `@page`, o componente não trataria as solicitações roteadas, mas o componente ainda poderia ser usado por outros componentes.</span><span class="sxs-lookup"><span data-stu-id="b0756-168">Without the `@page` directive, the component wouldn't handle routed requests, but the component could still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="b0756-169">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="b0756-169">Dependency injection</span></span>

<span data-ttu-id="b0756-170">Os serviços registrados com o provedor de serviços do aplicativo estão disponíveis para componentes por meio da [DI (injeção de dependência)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b0756-170">Services registered with the app's service provider are available to components via [dependency injection (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).</span></span> <span data-ttu-id="b0756-171">Os serviços podem ser injetados em um componente usando a diretiva `@inject`.</span><span class="sxs-lookup"><span data-stu-id="b0756-171">Services can be injected into a component using the `@inject` directive.</span></span>

<span data-ttu-id="b0756-172">Observe a implementação do componente `FetchData` em *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b0756-172">Take a look at the implementation of the `FetchData` component in *FetchData.cshtml*.</span></span> <span data-ttu-id="b0756-173">A diretiva `@inject` é usada para inserir uma instância [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) no componente.</span><span class="sxs-lookup"><span data-stu-id="b0756-173">The `@inject` directive is used to inject an [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) instance into the component.</span></span>

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

<span data-ttu-id="b0756-174">O componente `FetchData` usa o `HttpClient` injetado para recuperar dados JSON do servidor quando o componente é inicializado.</span><span class="sxs-lookup"><span data-stu-id="b0756-174">The `FetchData` component uses the injected `HttpClient` to retrieve JSON data from the server when the component is initialized.</span></span> <span data-ttu-id="b0756-175">Nos bastidores, o `HttpClient` fornecido pelo tempo de execução do Blazor é implementado usando a interop do JavaScript para chamar a API Fetch do navegador subjacente a fim de enviar a solicitação (de C#, é possível chamar qualquer API de navegador ou biblioteca JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b0756-175">Under the covers, the `HttpClient` provided by the Blazor runtime is implemented using JavaScript interop to call the underlying browser's Fetch API to send the request (from C#, it's possible to call any JavaScript library or browser API).</span></span> <span data-ttu-id="b0756-176">Os dados recuperados são desserializados na variável `forecasts` de C# como uma matriz de objetos `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="b0756-176">The retrieved data is deserialized into the `forecasts` C# variable as an array of `WeatherForecast` objects.</span></span>

```cshtml
@functions {
    WeatherForecast[] forecasts;

    protected override async Task OnInitAsync()
    {
        forecasts = await Http.GetJsonAsync<WeatherForecast[]>("/sample-data/weather.json");
    }

    class WeatherForecast
    {
        public DateTime Date { get; set; }
        public int TemperatureC { get; set; }
        public int TemperatureF { get; set; }
        public string Summary { get; set; }
    }
}
```

<span data-ttu-id="b0756-177">Um loop `@foreach` é usado para renderizar cada instância de previsão como uma linha na tabela de clima.</span><span class="sxs-lookup"><span data-stu-id="b0756-177">A `@foreach` loop is used to render each forecast instance as a row in the weather table.</span></span>

```cshtml
<table class="table">
    <thead>
        <tr>
            <th>Date</th>
            <th>Temp. (C)</th>
            <th>Temp. (F)</th>
            <th>Summary</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var forecast in forecasts)
        {
            <tr>
                <td>@forecast.Date.ToShortDateString()</td>
                <td>@forecast.TemperatureC</td>
                <td>@forecast.TemperatureF</td>
                <td>@forecast.Summary</td>
            </tr>
        }
    </tbody>
</table>
```

## <a name="build-a-todo-list"></a><span data-ttu-id="b0756-178">Criar uma lista de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="b0756-178">Build a todo list</span></span>

<span data-ttu-id="b0756-179">Adicione uma nova página ao aplicativo que implemente uma lista de tarefas pendentes simples.</span><span class="sxs-lookup"><span data-stu-id="b0756-179">Add a new page to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="b0756-180">Adicione um arquivo de texto vazio à pasta *Páginas* denominado *Todo.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b0756-180">Add an empty text file to the *Pages* folder named *Todo.cshtml*.</span></span>

1. <span data-ttu-id="b0756-181">Forneça a marcação inicial à página.</span><span class="sxs-lookup"><span data-stu-id="b0756-181">Provide the initial markup for the page.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. <span data-ttu-id="b0756-182">Adicione a página Tarefas Pendentes à barra de navegação atualizando *Shared/NavMenu.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b0756-182">Add the Todo page to the navigation bar by updating *Shared/NavMenu.cshtml*.</span></span> <span data-ttu-id="b0756-183">Adicione um `NavLink` à página de Tarefas Pendentes, incluindo a marcação de item de lista a seguir, abaixo dos itens de lista existentes.</span><span class="sxs-lookup"><span data-stu-id="b0756-183">Add a `NavLink` for the Todo page by adding the following list item markup below the existing list items.</span></span>

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. <span data-ttu-id="b0756-184">Atualize o aplicativo no navegador.</span><span class="sxs-lookup"><span data-stu-id="b0756-184">Refresh the app in the browser.</span></span> <span data-ttu-id="b0756-185">Veja a nova página Tarefas Pendentes.</span><span class="sxs-lookup"><span data-stu-id="b0756-185">See the new Todo page.</span></span>

    ![Início de tarefas pendentes do Blazor](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. <span data-ttu-id="b0756-187">Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará os itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="b0756-187">Add a *TodoItem.cs* file to the root of the project to hold a class to represent the todo items.</span></span>

1. <span data-ttu-id="b0756-188">Use o seguinte código de C# para a classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="b0756-188">Use the following C# code for the `TodoItem` class.</span></span>

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. <span data-ttu-id="b0756-189">Volte para o componente `Todo` em *Todo.cshtml* e adicione um campo para as tarefas pendentes em um bloco `@functions`.</span><span class="sxs-lookup"><span data-stu-id="b0756-189">Go back to the `Todo` component in *Todo.cshtml* and add a field for the todos in a `@functions` block.</span></span> <span data-ttu-id="b0756-190">O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="b0756-190">The `Todo` component uses this field to maintain the state of the todo list.</span></span>

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. <span data-ttu-id="b0756-191">Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista.</span><span class="sxs-lookup"><span data-stu-id="b0756-191">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>
    ```

1. <span data-ttu-id="b0756-192">O aplicativo requer os elementos de interface do usuário para adicionar tarefas pendentes à lista.</span><span class="sxs-lookup"><span data-stu-id="b0756-192">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="b0756-193">Adicione uma entrada de texto e um botão abaixo da lista.</span><span class="sxs-lookup"><span data-stu-id="b0756-193">Add a text input and a button below the list.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" />
    <button>Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. <span data-ttu-id="b0756-194">Atualize o navegador.</span><span class="sxs-lookup"><span data-stu-id="b0756-194">Refresh the browser.</span></span>

    ![Adicionar botão de tarefas pendentes](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    <span data-ttu-id="b0756-196">Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.</span><span class="sxs-lookup"><span data-stu-id="b0756-196">Nothing happens when the **Add todo** button is selected because no event handler is wired up to the button.</span></span>

1. <span data-ttu-id="b0756-197">Adicione um método `AddTodo` ao componente `Todo` e registre-o para cliques de botão usando o atributo `onclick`.</span><span class="sxs-lookup"><span data-stu-id="b0756-197">Add an `AddTodo` method to the `Todo` component and register it for button clicks using the `onclick` attribute.</span></span>

    ```cshtml
    <input placeholder="Something todo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();

        void AddTodo()
        {
            // Todo: Add the todo
        }
    }
    ```

    <span data-ttu-id="b0756-198">O método `AddTodo` de C# é chamado sempre que o botão é selecionado.</span><span class="sxs-lookup"><span data-stu-id="b0756-198">The `AddTodo` C# method is called every time the button is selected.</span></span>

1. <span data-ttu-id="b0756-199">Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` e associe-o ao valor da próxima entrada de texto usando o atributo `bind`.</span><span class="sxs-lookup"><span data-stu-id="b0756-199">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute.</span></span>

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. <span data-ttu-id="b0756-200">Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista.</span><span class="sxs-lookup"><span data-stu-id="b0756-200">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="b0756-201">Não deixe de limpar o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="b0756-201">Don't forget to clear the value of the text input by setting `newTodo` to an empty string.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

1. <span data-ttu-id="b0756-202">Atualize o navegador.</span><span class="sxs-lookup"><span data-stu-id="b0756-202">Refresh the browser.</span></span> <span data-ttu-id="b0756-203">Adicione algumas tarefas pendentes à lista de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="b0756-203">Add some todos to the todo list.</span></span>

    ![Adicionar tarefas pendentes](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. <span data-ttu-id="b0756-205">Por fim, o que é uma lista de tarefas pendentes sem caixas de seleção?</span><span class="sxs-lookup"><span data-stu-id="b0756-205">Lastly, what's a todo list without check boxes?</span></span> <span data-ttu-id="b0756-206">O texto do título para cada item de tarefa pendente também pode se tornar editável.</span><span class="sxs-lookup"><span data-stu-id="b0756-206">The title text for each todo item can be made editable as well.</span></span> <span data-ttu-id="b0756-207">Adicione uma entrada de caixa de seleção e uma entrada de texto para cada item de tarefa pendente e associe os valores às propriedades `Title` e `IsDone`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="b0756-207">Add a check box input and text input for each todo item and bind their values to the `Title` and `IsDone` properties, respectively.</span></span>

    ```cshtml
    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>
    ```

1. <span data-ttu-id="b0756-208">Para verificar se esses valores estão associados, atualize o cabeçalho `h1` para mostrar uma contagem do número de itens de tarefas pendentes que ainda não foram concluídos (`IsDone` é `false`).</span><span class="sxs-lookup"><span data-stu-id="b0756-208">To verify that these values are bound, update the `h1` header to show a count of the number of todo items that are not yet done (`IsDone` is `false`).</span></span>

    ```cshtml
    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
    ```

1. <span data-ttu-id="b0756-209">O componente `Todo` concluído deve ter a seguinte aparência:</span><span class="sxs-lookup"><span data-stu-id="b0756-209">The completed `Todo` component should look like this:</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

<span data-ttu-id="b0756-210">Atualize o aplicativo no navegador.</span><span class="sxs-lookup"><span data-stu-id="b0756-210">Refresh the app in the browser.</span></span> <span data-ttu-id="b0756-211">Tente adicionar alguns itens de tarefa pendente.</span><span class="sxs-lookup"><span data-stu-id="b0756-211">Try adding some todo items.</span></span>

![Lista de tarefas pendentes do Blazor concluída](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## <a name="publish-and-deploy"></a><span data-ttu-id="b0756-213">Publicar e implantar</span><span class="sxs-lookup"><span data-stu-id="b0756-213">Publish and deploy</span></span>

<span data-ttu-id="b0756-214">Usando o Visual Studio, faça o seguinte para publicar o aplicativo Blazor de Tarefas Pendentes no Azure:</span><span class="sxs-lookup"><span data-stu-id="b0756-214">When using Visual Studio, perform the following steps to publish the Todo Blazor app to Azure:</span></span>

1. <span data-ttu-id="b0756-215">Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções**, e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="b0756-215">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="b0756-216">Na caixa de diálogo **Escolher um destino de publicação**, selecione **Serviço de Aplicativo** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="b0756-216">In the **Pick a publish target** dialog, select **App Service** and **Create New**.</span></span> <span data-ttu-id="b0756-217">Selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="b0756-217">Select **Publish**.</span></span>

    ![Escolher um destino de publicação](build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. <span data-ttu-id="b0756-219">Na caixa de diálogo **Criar Serviço de Aplicativo**, escolha um nome para o aplicativo e selecione a assinatura, o grupo de recursos e o plano de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="b0756-219">In the **Create App Service** dialog, choose a name for the app and select the subscription, resource group, and hosting plan.</span></span> <span data-ttu-id="b0756-220">Selecione **Criar** para criar o serviço de aplicativo e publicar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b0756-220">Select **Create** to create the app service and publish the app.</span></span>

    ![Criar serviço de aplicativo](build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

<span data-ttu-id="b0756-222">Aguarde alguns minutos para que o aplicativo seja implantado.</span><span class="sxs-lookup"><span data-stu-id="b0756-222">Wait a minute or so for the app to be deployed.</span></span>

<span data-ttu-id="b0756-223">O aplicativo agora deve estar em execução no Azure.</span><span class="sxs-lookup"><span data-stu-id="b0756-223">The app should now be running in Azure.</span></span> <span data-ttu-id="b0756-224">Marque o item de tarefa pendente para criar seu primeiro aplicativo Blazor como *concluído*.</span><span class="sxs-lookup"><span data-stu-id="b0756-224">Mark the todo item to build your first Blazor app as *done*.</span></span> <span data-ttu-id="b0756-225">Bom trabalho!</span><span class="sxs-lookup"><span data-stu-id="b0756-225">Nice job!</span></span>

![Blazor no Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> <span data-ttu-id="b0756-227">Se não estiver usando o Visual Studio, publique o aplicativo Blazor em um prompt de comando no Windows, macOS ou Linux:</span><span class="sxs-lookup"><span data-stu-id="b0756-227">If not using Visual Studio, publish the Blazor app at a command prompt on Windows, macOS, or Linux:</span></span>
>
> ```console
> dotnet publish -c Release
> ```
>
> <span data-ttu-id="b0756-228">a implantação é criada na pasta */bin/Release/\<target-framework>/publish*.</span><span class="sxs-lookup"><span data-stu-id="b0756-228">The deployment is created in the */bin/Release/\<target-framework>/publish* folder.</span></span> <span data-ttu-id="b0756-229">Transfira o conteúdo da pasta *publish* para o servidor ou serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="b0756-229">Move the contents of the *publish* folder to the server or hosting service.</span></span>
>
> <span data-ttu-id="b0756-230">Confira mais informações no tópico [Hospedar e implantar](xref:host-and-deploy/razor-components/index#publish-the-app).</span><span class="sxs-lookup"><span data-stu-id="b0756-230">For more information, see the [Host and deploy](xref:host-and-deploy/razor-components/index#publish-the-app) topic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0756-231">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b0756-231">Additional resources</span></span>

<span data-ttu-id="b0756-232">Para obter um aplicativo Blazor de exemplo mais complexo, verifique o [exemplo Flight Finder](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) no GitHub.</span><span class="sxs-lookup"><span data-stu-id="b0756-232">For a more involved Blazor sample app, check out the [Flight Finder sample](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) on GitHub.</span></span>

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
