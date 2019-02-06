---
title: Componentes de exibição no ASP.NET Core
author: rick-anderson
description: Saiba como os componentes de exibição são usados no ASP.NET Core e como adicioná-los aos aplicativos.
ms.author: riande
ms.date: 1/30/2019
uid: mvc/views/view-components
ms.openlocfilehash: d0e528fcf9e20afee98e74fbc09c67b81e123e95
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428389"
---
# <a name="view-components-in-aspnet-core"></a>Componentes de exibição no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="view-components"></a>Componentes da exibição

Os componentes de exibição são semelhantes às exibições parciais, mas são muito mais eficientes. Os componentes de exibição não usam o model binding e dependem apenas dos dados fornecidos durante uma chamada a eles. Este artigo foi elaborado com controladores e modos de exibição, mas os componentes da exibição também funcionam com o Razor Pages.

Um componente de exibição:

* Renderiza uma parte em vez de uma resposta inteira.
* Inclui os mesmos benefícios de capacidade de teste e separação de interesses e encontrados entre um controlador e uma exibição.
* Pode ter parâmetros e uma lógica de negócios.
* É geralmente invocado em uma página de layout.

Os componentes de exibição destinam-se a qualquer momento em que há uma lógica de renderização reutilizável muito complexa para uma exibição parcial, como:

* Menus de navegação dinâmica
* Nuvem de marcas (na qual ela consulta o banco de dados)
* Painel de logon
* Carrinho de compras
* Artigos publicados recentemente
* Conteúdo da barra lateral em um blog típico
* Um painel de logon que é renderizado em cada página e mostra os links para logoff ou logon, dependendo do estado de logon do usuário

Um componente de exibição consiste em duas partes: a classe (normalmente derivada de [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) e o resultado que ele retorna (normalmente, uma exibição). Assim como os controladores, um componente de exibição pode ser um POCO, mas a maioria dos desenvolvedores desejará aproveitar os métodos e as propriedades disponíveis com a derivação de `ViewComponent`.

## <a name="creating-a-view-component"></a>Criando um componente de exibição

Esta seção contém os requisitos de alto nível para a criação de um componente de exibição. Mais adiante neste artigo, examinaremos cada etapa em detalhes e criaremos um componente de exibição.

### <a name="the-view-component-class"></a>A classe de componente de exibição

Uma classe de componente de exibição pode ser criada por um dos seguintes:

* Derivação de *ViewComponent*
* Decoração de uma classe com o atributo `[ViewComponent]` ou derivação de uma classe com o atributo `[ViewComponent]`
* Criação de uma classe em que o nome termina com o sufixo *ViewComponent*

Assim como os controladores, os componentes de exibição precisam ser classes públicas, não aninhadas e não abstratas. O nome do componente de exibição é o nome da classe com o sufixo "ViewComponent" removido. Também pode ser especificado de forma explícita com a propriedade `ViewComponentAttribute.Name`.

Uma classe de componente de exibição:

* Dá suporte total à [injeção de dependência do construtor](../../fundamentals/dependency-injection.md)

* Não participa do ciclo de vida do controlador, o que significa que não é possível usar [filtros](../controllers/filters.md) em um componente de exibição

### <a name="view-component-methods"></a>Métodos de componente de exibição

Um componente de exibição define sua lógica em um método `InvokeAsync` que retorna um `Task<IViewComponentResult>` ou em um método `Invoke` síncrono que retorna um `IViewComponentResult`. Os parâmetros são recebidos diretamente da invocação do componente de exibição, não do model binding. Um componente de exibição nunca manipula uma solicitação diretamente. Normalmente, um componente de exibição inicializa um modelo e passa-o para uma exibição chamando o método `View`. Em resumo, os métodos de componente de exibição:

* Definem um método `InvokeAsync` que retorna um `Task<IViewComponentResult>` ou um método `Invoke` síncrono que retorna um `IViewComponentResult`.
* Normalmente, inicializam um modelo e o passam para uma exibição chamando o método `ViewComponent` `View`.
* Os parâmetros são recebidos do método de chamada, não do HTTP. Não há nenhum model binding.
* Não são acessíveis diretamente como um ponto de extremidade HTTP. Eles são invocados no código (normalmente, em uma exibição). Um componente de exibição nunca manipula uma solicitação.
* São sobrecarregados na assinatura, em vez de nos detalhes da solicitação HTTP atual.

### <a name="view-search-path"></a>Caminho de pesquisa de exibição

O tempo de execução pesquisa a exibição nos seguintes caminhos:

* /Views/{Nome do Controlador}/Components/{Nome do Componente da Exibição}/{Nome da Exibição}
* /Views/Shared/Components/{Nome do Componente da Exibição}/{Nome da Exibição}
* /Pages/Shared/Components/{Nome do Componente da Exibição}/{Nome da Exibição}

O caminho de pesquisa se aplica a projetos usando controladores + exibições e Razor Pages.

O nome de exibição padrão de um componente de exibição é *Default*, o que significa que o arquivo de exibição geralmente será nomeado *Default.cshtml*. Especifique outro nome de exibição ao criar o resultado do componente de exibição ou ao chamar o método `View`.

Recomendamos que você nomeie o arquivo de exibição *Default.cshtml* e use o caminho *Views/Shared/Components/{Nome do Componente da Exibição}/{Nome da Exibição}*. O componente de exibição `PriorityList` usado nesta amostra usa *Views/Shared/Components/PriorityList/Default.cshtml* como a exibição do componente de exibição.

## <a name="invoking-a-view-component"></a>Invocando um componente de exibição

Para usar o componente de exibição, chame o seguinte em uma exibição:

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

Os parâmetros serão passados para o método `InvokeAsync`. O componente de exibição `PriorityList` desenvolvido no artigo é invocado por meio do arquivo de exibição *Views/ToDo/Index.cshtml*. A seguir, o método `InvokeAsync` é chamado com dois parâmetros:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>Invocando um componente de exibição como um Auxiliar de Marca

Para o ASP.NET Core 1.1 e superior, invoque um componente de exibição como um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro):

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Os parâmetros de classe e de método na formatação Pascal Case para Auxiliares de Marcas são convertidos em [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101). Para invocar um componente de exibição, o Auxiliar de Marca usa o elemento `<vc></vc>`. O componente de exibição é especificado da seguinte maneira:

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

Para usar um componente de exibição como um Auxiliar de Marca, registre o assembly que contém o componente de exibição usando a diretiva `@addTagHelper`. Se seu componente de exibição estiver em um assembly chamado `MyWebApp`, adicione a seguinte diretiva ao arquivo *_ViewImports.cshtml*:

```cshtml
@addTagHelper *, MyWebApp
```

É possível registrar um componente de exibição como um Auxiliar de Marca a qualquer arquivo que referencie o componente de exibição. Consulte [Gerenciando o escopo do Auxiliar de Marca](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) para obter mais informações sobre como registrar Auxiliares de Marca.

O método `InvokeAsync` usado neste tutorial:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

Na marcação do Auxiliar de Marca:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

Na amostra acima, o componente de exibição `PriorityList` torna-se `priority-list`. Os parâmetros para o componente de exibição são passados como atributos em kebab case.

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>Invocando um componente de exibição diretamente em um controlador

Os componentes de exibição normalmente são invocados em uma exibição, mas você pode invocá-los diretamente em um método do controlador. Embora os componentes de exibição não definam pontos de extremidade como controladores, você pode implementar com facilidade uma ação do controlador que retorna o conteúdo de um `ViewComponentResult`.

Neste exemplo, o componente de exibição é chamado diretamente no controlador:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>Passo a passo: Como criar um componente de exibição simples

[Baixe](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), compile e teste o código inicial. É um projeto simples com um controlador `ToDo` que exibe uma lista de itens *ToDo*.

![Lista de ToDos](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>Adicionar uma classe ViewComponent

Crie uma pasta *ViewComponents* e adicione a seguinte classe `PriorityListViewComponent`:

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

Observações sobre o código:

* As classes de componente de exibição podem ser contidas em **qualquer** pasta do projeto.
* Como o nome da classe PriorityList**ViewComponent** termina com o sufixo **ViewComponent**, o tempo de execução usará a cadeia de caracteres "PriorityList" ao referenciar o componente de classe em uma exibição. Explicarei isso mais detalhadamente mais adiante.
* O atributo `[ViewComponent]` pode alterar o nome usado para referenciar um componente de exibição. Por exemplo, poderíamos nomear a classe `XYZ` e aplicar o atributo `ViewComponent`:

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* O atributo `[ViewComponent]` acima instrui o seletor de componente de exibição a usar o nome `PriorityList` ao procurar as exibições associadas ao componente e a usar a cadeia de caracteres "PriorityList" ao referenciar o componente de classe em uma exibição. Explicarei isso mais detalhadamente mais adiante.
* O componente usa a [injeção de dependência](../../fundamentals/dependency-injection.md) para disponibilizar o contexto de dados.
* `InvokeAsync` expõe um método que pode ser chamado em uma exibição e pode usar um número arbitrário de argumentos.
* O método `InvokeAsync` retorna o conjunto de itens `ToDo` que atendem aos parâmetros `isDone` e `maxPriority`.

### <a name="create-the-view-component-razor-view"></a>Criar a exibição do Razor do componente de exibição

* Crie a pasta *Views/Shared/Components*. Essa pasta **deve** nomeada *Components*.

* Crie a pasta *Views/Shared/Components/PriorityList*. Esse nome de pasta deve corresponder ao nome da classe do componente de exibição ou ao nome da classe menos o sufixo (se seguimos a convenção e usamos o sufixo *ViewComponent* no nome da classe). Se você usou o atributo `ViewComponent`, o nome da classe precisa corresponder à designação de atributo.

* Crie uma exibição do Razor *Views/Shared/Components/PriorityList/Default.cshtml*: [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   A exibição do Razor usa uma lista de `TodoItem` e exibe-os. Se o método `InvokeAsync` do componente de exibição não passar o nome da exibição (como em nossa amostra), *Default* será usado como o nome da exibição, por convenção. Mais adiante no tutorial, mostrarei como passar o nome da exibição. Para substituir o estilo padrão de um controlador específico, adicione uma exibição à pasta de exibição específica do controlador (por exemplo, *Views/ToDo/Components/PriorityList/Default.cshtml)*.

    Se o componente de exibição for específico do controlador, adicione-o à pasta específica do controlador (*Views/ToDo/Components/PriorityList/Default.cshtml*).

* Adicione um `div` que contenha uma chamada para o componente da lista de prioridades à parte inferior do arquivo *Views/ToDo/index.cshtml*:

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

A marcação `@await Component.InvokeAsync` mostra a sintaxe para chamar componentes de exibição. O primeiro argumento é o nome do componente que queremos invocar ou chamar. Os parâmetros seguintes são passados para o componente. `InvokeAsync` pode usar um número arbitrário de argumentos.

Teste o aplicativo. A seguinte imagem mostra a lista ToDo e os itens de prioridade:

![lista de tarefas pendentes e itens de prioridade](view-components/_static/pi.png)

Também chame o componente de exibição diretamente no controlador:

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![itens de prioridade da ação IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>Especificando um nome de exibição

Um componente de exibição complexo pode precisar especificar uma exibição não padrão em algumas condições. O código a seguir mostra como especificar a exibição "PVC" no método `InvokeAsync`. Atualize o método `InvokeAsync` na classe `PriorityListViewComponent`.

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

Copie o arquivo *Views/Shared/Components/PriorityList/Default.cshtml* para uma exibição nomeada *Views/Shared/Components/PriorityList/PVC.cshtml*. Adicione um cabeçalho para indicar que a exibição PVC está sendo usada.

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

Atualize *Views/ToDo/Index.cshtml*:

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

Execute o aplicativo e verifique a exibição PVC.

![Componente de exibição de prioridade](view-components/_static/pvc.png)

Se a exibição PVC não é renderizada, verifique se você está chamando o componente de exibição com uma prioridade igual a 4 ou superior.

### <a name="examine-the-view-path"></a>Examinar o caminho de exibição

* Altere o parâmetro de prioridade para três ou menos para que a exibição de prioridade não seja retornada.
* Renomeie temporariamente *Views/ToDo/Components/PriorityList/Default.cshtml* como *1Default.cshtml*.
* Teste o aplicativo. Você obterá o seguinte erro:

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* Copie *Views/ToDo/Components/PriorityList/1Default.cshtml* para *Views/Shared/Components/PriorityList/Default.cshtml*.
* Adicione uma marcação ao componente de exibição ToDo *Shared* para indicar que a exibição foi obtida da pasta *Shared*.
* Teste o componente de exibição **Shared**.

![Saída de ToDo com o componente de exibição Shared](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a>Evitando cadeias de caracteres mágicas

Se deseja obter segurança em tempo de compilação, substitua o nome do componente de exibição embutido em código pelo nome da classe. Crie o componente de exibição sem o sufixo "ViewComponent":

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

Adicione uma instrução `using` ao arquivo de exibição do Razor e use o operador `nameof`:

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>Executar o trabalho síncrono

A estrutura manipulará a invocação de um método `Invoke` síncrono se não for necessário realizar o trabalho assíncrono. O método a seguir cria um componente de exibição `Invoke` síncrono:

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

O arquivo do Razor do componente de exibição lista as cadeias de caracteres passadas para o método `Invoke` (*Views/Home/Components/PriorityList/Default.cshtml*):

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

O componente de exibição é invocado em um arquivo do Razor (por exemplo, *Views/Home/Index.cshtml*) usando uma das seguintes abordagens:

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro)

Para usar a abordagem <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, chame `Component.InvokeAsync`:

::: moniker-end

::: moniker range="< aspnetcore-1.1"

O componente de exibição é invocado em um arquivo do Razor (por exemplo, *Views/Home/Index.cshtml*) com <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.

Chame `Component.InvokeAsync`:

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

Para usar o Auxiliar de Marca, registre o assembly que contém o Componente de exibição que usa a diretiva `@addTagHelper` (o componente de exibição está em um assembly chamado `MyWebApp`):

```cshtml
@addTagHelper *, MyWebApp
```

Use o componente de exibição Auxiliar de Marca no arquivo de marcação do Razor:

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```
::: moniker-end

A assinatura do método de `PriorityList.Invoke` é síncrona, mas o Razor localiza e chama o método com `Component.InvokeAsync` no arquivo de marcação.

## <a name="additional-resources"></a>Recursos adicionais

* [Injeção de dependência em exibições](xref:mvc/views/dependency-injection)
