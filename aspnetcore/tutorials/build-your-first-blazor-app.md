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
# <a name="build-your-first-blazor-app"></a>Como criar seu primeiro aplicativo Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Este tutorial mostra como criar e modificar um aplicativo Blazor.

Siga as orientações no artigo <xref:blazor/get-started> para criar um projeto Blazor para este tutorial. Nomeie o projeto como *ToDoList*.

## <a name="build-components"></a>Componentes do build

1. Navegue até cada uma das três páginas do aplicativo na pasta *Pages*: Início, Contador e Buscar dados. Essas páginas são implementadas pelos arquivos de componente Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.

1. Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página. A incrementação de um contador em uma página da Web normalmente exige JavaScript, mas o Blazor fornece uma abordagem melhor usando C#.

1. Examine a implementação do componente `Counter` no arquivo *Counter.razor*.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   A interface do usuário do componente `Counter` é definida usando HTML. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor). A marcação HTML e a lógica de renderização de C# são convertidas em uma classe de componente no momento da compilação. O nome da classe .NET gerada corresponde ao nome do arquivo.

   Os membros da classe de componente são definidos em um bloco `@code`. No bloco `@code`, o estado do componente (propriedades, campos) e os métodos são especificados para manipulação de eventos ou para definição de outra lógica de componente. Assim, esses membros são usados como parte da lógica de renderização do componente e para manipulação de eventos.

   Quando o botão **Clique aqui** botão é selecionado:

   * O manipulador `onclick` registrado do componente `Counter` é chamado (o método `IncrementCount`).
   * O componente `Counter` regenera sua árvore de renderização.
   * A nova árvore de renderização é comparada à anterior.
   * Apenas as modificações ao DOM (Modelo de Objeto do Documento) são aplicadas. A contagem exibida é atualizada.

1. Modifique a lógica de C# do componente `Counter` para que o incremento da contagem seja por dois em vez de um.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Recompile e execute o aplicativo para ver as alterações. Selecione o botão **Clique aqui**. O contador incrementa em dois.

## <a name="use-components"></a>Usar componentes

Inclua um componente em outro componente usando uma sintaxe HTML.

1. Adicione o componente `Counter` no componente `Index` do aplicativo adicionando um elemento `<Counter />` ao componente `Index` (*Index.razor*).

   Se você estiver usando Webassembly mais experiente para essa experiência, um `SurveyPrompt` componente será usado `Index` pelo componente. Substitua o elemento `<SurveyPrompt>` pelo elemento `<Counter />`. Se você estiver usando um aplicativo de servidor mais experiente para essa experiência, adicione `<Counter />` o elemento `Index` ao componente:

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Recompile e execute o aplicativo. O componente `Index` tem seu próprio contador.

## <a name="component-parameters"></a>Parâmetros do componente

Componentes também podem ter parâmetros. Os parâmetros de componente são definidos usando propriedades públicas na classe de componente `[Parameter]` com o atributo. Use atributos para especificar argumentos para um componente na marcação.

1. Atualizar o código C# do componente `@code`:

   * Adicione uma propriedade `IncrementAmount` pública com o `[Parameter]` atributo.
   * Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Especifique um parâmetro `IncrementAmount` no elemento `<Counter>` do componente `Index` usando um atributo. Defina o valor para incrementar o contador em 10.

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Recarregue o componente `Index`. O contador é incrementado em dez sempre que o botão **Clique aqui** é selecionado. O contador no componente `Counter` continua a incrementar em um.

## <a name="route-to-components"></a>Rotear para componentes

A diretiva `@page` no início do arquivo *Counter.razor* especifica que esse componente `Counter` é um ponto de extremidade de roteamento. O componente `Counter` manipula solicitações enviadas para `/counter`. Sem a diretiva `@page`, o componente não lidará com as solicitações roteadas, mas ele ainda poderá ser usado por outros componentes.

## <a name="dependency-injection"></a>Injeção de dependência

Se estiver trabalhando com um aplicativo de servidor mais incrivelmente `WeatherForecastService` , o serviço será registrado como um `Startup.ConfigureServices` [singleton](xref:fundamentals/dependency-injection#service-lifetimes) no. Uma instância do serviço está disponível em todo o aplicativo por meio de [injeção de dependência (di)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

A `@inject` diretiva é usada para injetar a instância `WeatherForecastService` do serviço no `FetchData` componente.

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

O componente `FetchData` usa o serviço injetado, como `ForecastService`, para recuperar uma matriz de objetos `WeatherForecast`:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

Se estiver trabalhando com um aplicativo Webassembly de mais `HttpClient` alto, o é injetado para obter dados de previsão do tempo do arquivo *Weather. JSON* na pasta *wwwroot/Sample-data* .

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Um loop [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) é usado para renderizar cada uma das instâncias de previsão do tempo como uma linha na tabela de dados do tempo:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Criar uma lista de tarefas pendentes

Adicione um novo componente ao aplicativo que implemente uma lista de tarefas pendentes simples.

1. Adicione um arquivo vazio chamado *Todo.razor* ao aplicativo na pasta *Pages*:

1. Forneça a marcação inicial do componente:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Adicione o componente `Todo` à barra de navegação.

   O componente `NavMenu` (*Shared/NavMenu.razor*) é usado no layout do aplicativo. Layouts são componentes que permitem que você evite a duplicação de conteúdo no aplicativo.

   Adicione um elemento `<NavLink>` para o componente `Todo` adicionando a seguinte marcação de item da lista abaixo dos itens existentes no arquivo *Shared/NavMenu.razor*:

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Recompile e execute o aplicativo. Visite a nova página de Tarefas Pendentes para confirmar se o link para o componente `Todo` está funcionando.

1. Adicione um arquivo *TodoItem.cs* à raiz do projeto para manter uma classe que representará um item de tarefa pendente. Use o seguinte código C# para a classe `TodoItem`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Retorne para o componente `Todo` (*Pages/Todo.razor*):

   * Adicione um campo para os itens de tarefas pendentes em um bloco `@code`. O componente `Todo` usa esse campo para manter o estado da lista de tarefas pendentes.
   * Adicione marcação da lista não ordenada e um loop `foreach` para renderizar cada item de tarefa pendente como um item de lista (`<li>`).

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. O aplicativo requer elementos de interface do usuário para adicionar itens de tarefas à lista. Adicione uma entrada de texto (`<input>`) e um botão (`<button>`) abaixo da lista não ordenada (`<ul>...</ul>`):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Recompile e execute o aplicativo. Nada acontece quando o botão **Adicionar tarefas pendentes** é selecionado porque nenhum manipulador de eventos está conectado ao botão.

1. Adicione um método `AddTodo` ao componente `Todo` e registre-o para seleções de botão usando o atributo `@onclick`. O método C# `AddTodo` é chamado quando o botão é selecionado:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Para obter o título do novo item de tarefas pendentes, adicione um campo de cadeia de caracteres `newTodo` na parte superior de `@code`, bloqueie e associe-o ao valor da próxima entrada de texto usando o atributo `bind` no elemento `<input>`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Atualize o método `AddTodo` para adicionar o `TodoItem` com o título especificado à lista. Limpe o valor da entrada de texto configurando `newTodo` para uma cadeia de caracteres vazia:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Recompile e execute o aplicativo. Adicione alguns itens de tarefas à lista de tarefas para testar o novo código.

1. O texto do título de cada item de tarefa pode ser editável, e uma caixa de seleção pode ajudar o usuário a manter o controle dos itens concluídos. Adicione uma entrada de caixa de seleção para cada item de tarefa pendente e associe seu valor à propriedade `IsDone`. Altere `@todo.Title` para um elemento `<input>` associado a `@todo.Title`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Para verificar se esses valores estão associados, atualize o cabeçalho `<h1>` para mostrar uma contagem do número de itens de tarefa pendente que não estão concluídos (`IsDone` é `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. O componente `Todo` concluído (*Pages/Todo.razor*):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Recompile e execute o aplicativo. Adicione itens de tarefa pendente para testar o novo código.

> [!div class="nextstepaction"]
> <xref:blazor/components>
