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
# <a name="build-your-first-blazor-app"></a>Como criar seu primeiro aplicativo Blazor

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Neste tutorial, você cria um aplicativo Blazor passo a passo e aprende rapidamente sobre os recursos básicos da estrutura do Razor Components.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([como baixar](xref:index#how-to-download-a-sample)). Veja o tópico [Introdução](xref:razor-components/get-started) para conhecer os pré-requisitos.

Para criar o projeto no Visual Studio:

1. Selecione **Arquivo** > **Novo** > **Projeto**. Selecione **Web** > **Aplicativo Web ASP.NET Core**. Atribua o nome "BlazorApp1" ao projeto no campo **Nome**. Selecione **OK**.

    ![Novo projeto ASP.NET Core](build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. A caixa de diálogo **Novo Aplicativo Web ASP.NET Core** é exibida. Verifique se **.NET Core** está selecionado na parte superior. Selecione **ASP.NET Core 2.1**. Escolha o modelo **Blazor** e selecione **OK**.

    ![Caixa de diálogo Novo aplicativo Blazor](build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. Após a criação do projeto, pressione **Ctrl+F5** para executar o aplicativo *sem o depurador*. A execução com o depurador (**F5**) não tem suporte no momento.

> [!NOTE]
> Se não estiver usando o Visual Studio, crie o aplicativo Blazor em um prompt de comando no Windows, macOS ou Linux:
>
> ```console
> dotnet new --install "Microsoft.AspNetCore.Blazor.Templates"
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> Navegue até o aplicativo usando o endereço do localhost e a porta fornecidos na saída da janela do console, após a execução de `dotnet run`. Use **Ctrl+C** na janela do console para encerrar o aplicativo.

O aplicativo Blazor é executado no navegador:

![Home page do aplicativo Blazor](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## <a name="build-components"></a>Componentes do build

1. Navegue para cada uma das três páginas do aplicativo: Início, Contador e Buscar dados.

    Essas três páginas são implementadas por três arquivos Razor na pasta *Páginas*: *Index.cshtml*, *Counter.cshtml* e *FetchData.cshtml*. Cada um desses arquivos implementa um componente que é compilado e executado pelo cliente no navegador.

1. Selecione o botão na página Contador.

    ![Home page do aplicativo Blazor](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    Toda vez que o botão é selecionado, o contador é incrementado sem uma atualização de página. Normalmente, esse tipo de comportamento do cliente é tratado no JavaScript. Aqui, ele é implementado em C# e .NET pelo componente `Counter`.

1. Observe a implementação do componente `Counter` no arquivo *Counter.cshtml*:

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

    A interface do usuário do componente `Counter` é definida usando HTML normal. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor). A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação. O nome da classe .NET gerada corresponde ao nome do arquivo.

    Os membros da classe de componente são definidos em um bloco `@functions`. No bloco `@functions`, o estado do componente (propriedades, campos) e os métodos podem ser especificados para manipulação de eventos ou para definição de outra lógica de componente. Assim, esses membros podem ser usados como parte da lógica de renderização do componente e da manipulação de eventos.

    Quando o botão é selecionado, o manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`), e o componente `Counter` regenera a respectiva árvore de renderização. O Blazor compara a nova árvore de renderização em relação à anterior e aplica todas as modificações ao DOM (Modelo de Objeto do Documento) do navegador. A contagem exibida é atualizada.

1. Atualize a marcação para o componente `Counter` de modo a tornar o cabeçalho de nível superior mais *instigante*.

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. Modifique também a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. Atualize a página do contador no navegador para ver as alterações.

    ![Contador instigante](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## <a name="use-components"></a>Usar componentes

Depois de definir um componente, ele poderá ser usado para implementar outros componentes. A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.

1. Adicione um componente `Counter` à home page do aplicativo (*index.cshtml*).

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. Atualize a home page no navegador. Observe a instância separada do componente `Counter` na home page.

    ![Home page do Blazer com contador](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## <a name="component-parameters"></a>Parâmetros do componente

Os componentes também podem ter parâmetros que são definidos usando as propriedades privadas na classe de componente decorada com `[Parameter]`. Use atributos para especificar argumentos para um componente na marcação. 

1. Atualize o componente `Counter` para ter um parâmetro `IncrementAmount` que seja padronizado como 1.

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
    > No Visual Studio, você pode adicionar um parâmetro de componente rapidamente usando o snippet de código `para`. Digite `para` e pressione a tecla `Tab` duas vezes.

1. Na home page (*index.cshtml*), altere o valor do incremento do `Counter` para 10 configurando um atributo que corresponda ao nome da propriedade do componente de `IncrementCount`.

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. Recarregue a página.

    O incremento do contador na home page agora é 10, enquanto o incremento do contador na página Contador ainda é 1.

    ![Contagem de Blazor por dez](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## <a name="route-to-components"></a>Rotear para componentes

A diretiva `@page` no início do arquivo *Counter.cshtml* especifica que esse componente é uma página para a qual as solicitações podem ser roteadas. Especificamente, o componente `Counter` manipula solicitações enviadas para `/Counter`. Sem a diretiva `@page`, o componente não trataria as solicitações roteadas, mas o componente ainda poderia ser usado por outros componentes.

## <a name="dependency-injection"></a>Injeção de dependência

Os serviços registrados com o provedor de serviços do aplicativo estão disponíveis para componentes por meio da [DI (injeção de dependência)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Os serviços podem ser injetados em um componente usando a diretiva `@inject`.

Observe a implementação do componente `FetchData` em *FetchData.cshtml*. A diretiva `@inject` é usada para inserir uma instância [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) no componente.

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

O componente `FetchData` usa o `HttpClient` injetado para recuperar dados JSON do servidor quando o componente é inicializado. Nos bastidores, o `HttpClient` fornecido pelo tempo de execução do Blazor é implementado usando a interop do JavaScript para chamar a API Fetch do navegador subjacente a fim de enviar a solicitação (de C#, é possível chamar qualquer API de navegador ou biblioteca JavaScript). Os dados recuperados são desserializados na variável `forecasts` de C# como uma matriz de objetos `WeatherForecast`.

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

Um loop `@foreach` é usado para renderizar cada instância de previsão como uma linha na tabela de clima.

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

## <a name="build-a-todo-list"></a>Criar uma lista de tarefas pendentes

Adicione uma nova página ao aplicativo que implemente uma lista de tarefas pendentes simples.

1. Adicione um arquivo de texto vazio à pasta *Páginas* denominado *Todo.cshtml*.

1. Forneça a marcação inicial à página.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. Adicione a página Tarefas Pendentes à barra de navegação atualizando *Shared/NavMenu.cshtml*. Adicione um `NavLink` à página de Tarefas Pendentes, incluindo a marcação de item de lista a seguir, abaixo dos itens de lista existentes.

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. Atualize o aplicativo no navegador. Veja a nova página Tarefas Pendentes.

    ![Início de tarefas pendentes do Blazor](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará os itens de tarefas pendentes.

1. Use o seguinte código de C# para a classe `TodoItem`.

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. Volte para o componente `Todo` em *Todo.cshtml* e adicione um campo para as tarefas pendentes em um bloco `@functions`. O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista.

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

1. O aplicativo requer os elementos de interface do usuário para adicionar tarefas pendentes à lista. Adicione uma entrada de texto e um botão abaixo da lista.

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

1. Atualize o navegador.

    ![Adicionar botão de tarefas pendentes](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.

1. Adicione um método `AddTodo` ao componente `Todo` e registre-o para cliques de botão usando o atributo `onclick`.

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

    O método `AddTodo` de C# é chamado sempre que o botão é selecionado.

1. Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` e associe-o ao valor da próxima entrada de texto usando o atributo `bind`.

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista. Não deixe de limpar o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia.

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

1. Atualize o navegador. Adicione algumas tarefas pendentes à lista de tarefas pendentes.

    ![Adicionar tarefas pendentes](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. Por fim, o que é uma lista de tarefas pendentes sem caixas de seleção? O texto do título para cada item de tarefa pendente também pode se tornar editável. Adicione uma entrada de caixa de seleção e uma entrada de texto para cada item de tarefa pendente e associe os valores às propriedades `Title` e `IsDone`, respectivamente.

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

1. Para verificar se esses valores estão associados, atualize o cabeçalho `h1` para mostrar uma contagem do número de itens de tarefas pendentes que ainda não foram concluídos (`IsDone` é `false`).

    ```cshtml
    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
    ```

1. O componente `Todo` concluído deve ter a seguinte aparência:

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

Atualize o aplicativo no navegador. Tente adicionar alguns itens de tarefa pendente.

![Lista de tarefas pendentes do Blazor concluída](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## <a name="publish-and-deploy"></a>Publicar e implantar

Usando o Visual Studio, faça o seguinte para publicar o aplicativo Blazor de Tarefas Pendentes no Azure:

1. Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções**, e selecione **Publicar**.

1. Na caixa de diálogo **Escolher um destino de publicação**, selecione **Serviço de Aplicativo** e **Criar Novo**. Selecione **Publicar**.

    ![Escolher um destino de publicação](build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. Na caixa de diálogo **Criar Serviço de Aplicativo**, escolha um nome para o aplicativo e selecione a assinatura, o grupo de recursos e o plano de hospedagem. Selecione **Criar** para criar o serviço de aplicativo e publicar o aplicativo.

    ![Criar serviço de aplicativo](build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

Aguarde alguns minutos para que o aplicativo seja implantado.

O aplicativo agora deve estar em execução no Azure. Marque o item de tarefa pendente para criar seu primeiro aplicativo Blazor como *concluído*. Bom trabalho!

![Blazor no Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> Se não estiver usando o Visual Studio, publique o aplicativo Blazor em um prompt de comando no Windows, macOS ou Linux:
>
> ```console
> dotnet publish -c Release
> ```
>
> a implantação é criada na pasta */bin/Release/\<target-framework>/publish*. Transfira o conteúdo da pasta *publish* para o servidor ou serviço de hospedagem.
>
> Confira mais informações no tópico [Hospedar e implantar](xref:host-and-deploy/razor-components/index#publish-the-app).

## <a name="additional-resources"></a>Recursos adicionais

Para obter um aplicativo Blazor de exemplo mais complexo, verifique o [exemplo Flight Finder](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) no GitHub.

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
