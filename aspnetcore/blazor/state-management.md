---
title: Gerenciamento de estado de Blazor ASP.NET Core
author: guardrex
description: Saiba como persistir o estado em aplicativos do Blazor Server.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/state-management
ms.openlocfilehash: 38ee5fccdf476f08c9f39d01b53c81b48eea04bf
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317179"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>Gerenciamento de estado de Blazor ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

o Blazor Server é uma estrutura de aplicativo com estado. Na maioria das vezes, o aplicativo mantém uma conexão contínua com o servidor. O estado do usuário é mantido na memória do servidor em um *circuito*. 

Exemplos de estado mantido para o circuito de um usuário incluem:

* A interface do usuário renderizada&mdash;hierarquia de instâncias de componente e sua saída de renderização mais recente.
* Os valores de quaisquer campos e propriedades em instâncias de componente.
* Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.

> [!NOTE]
> Este artigo aborda a persistência de estado em aplicativos do Blazor Server. Blazor aplicativos Webassembly podem aproveitar [a persistência de estado do lado do cliente no navegador](#client-side-in-the-browser) , mas exigem soluções personalizadas ou pacotes de terceiros além do escopo deste artigo.

## <a name="opno-locblazor-circuits"></a>circuitos de Blazor

Se um usuário tiver uma perda de conexão de rede temporária, Blazor tentar reconectar o usuário ao seu circuito original para que ele possa continuar a usar o aplicativo. No entanto, a reconexão de um usuário ao seu circuito original na memória do servidor nem sempre é possível:

* O servidor não pode manter um circuito desconectado para sempre. O servidor deve liberar um circuito desconectado após um tempo limite ou quando o servidor está sob pressão de memória.
* No multisservidor, ambientes de implantação com balanceamento de carga, qualquer solicitação de processamento de servidor pode se tornar indisponível em um determinado momento. Os servidores individuais podem falhar ou ser removidos automaticamente quando não forem mais necessários para lidar com o volume geral de solicitações. O servidor original pode não estar disponível quando o usuário tenta se reconectar.
* O usuário pode fechar e reabrir o navegador ou recarregar a página, o que remove qualquer estado mantido na memória do navegador. Por exemplo, os valores definidos por meio de chamadas de interoperabilidade do JavaScript são perdidos.

Quando um usuário não pode ser reconectado ao seu circuito original, o usuário recebe um novo circuito com um estado vazio. Isso é equivalente a fechar e reabrir um aplicativo de área de trabalho.

## <a name="preserve-state-across-circuits"></a>Preservar estado entre circuitos

Em alguns cenários, a preservação do estado entre circuitos é desejável. Um aplicativo pode reter dados importantes para um usuário se:

* O servidor Web fica indisponível.
* O navegador do usuário é forçado a iniciar um novo circuito com um novo servidor Web.

Em geral, a manutenção de estado entre circuitos se aplica a cenários em que os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.

Para preservar o estado além de um único circuito, *não armazene apenas os dados na memória do servidor*. O aplicativo deve manter os dados em algum outro local de armazenamento. A persistência de estado não é automática&mdash;você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.

A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar. Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:

* Formulários da webstep &ndash; é demorado para um usuário reinserir dados para várias etapas concluídas de um processo com vários passos se seu estado for perdido. Um usuário perde o estado nesse cenário se ele navega para fora do formulário com várias etapas e retorna para o formulário mais tarde.
* O carrinho de compras &ndash; qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido. Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.

Normalmente, não é necessário preservar o estado de recriação fácil, como o nome de usuário inserido em uma caixa de diálogo de entrada que não foi enviada.

> [!IMPORTANT]
> Um aplicativo só pode persistir o *estado do aplicativo*. As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização. Os componentes e as árvores de renderização geralmente não são serializáveis. Para persistir algo semelhante ao estado da interface do usuário, como os nós expandidos de um TreeView, o aplicativo deve ter um código personalizado para modelar o comportamento como um estado do aplicativo serializável.

## <a name="where-to-persist-state"></a>Onde persistir o estado

Existem três localizações comuns para o estado persistente em um aplicativo do Blazor Server. Cada abordagem é mais adequada para cenários diferentes e tem diferentes limitações:

* [Lado do servidor em um banco de dados](#server-side-in-a-database)
* [URL](#url)
* [Lado do cliente no navegador](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>Lado do servidor em um banco de dados

Para persistência de dados permanentes ou para quaisquer dados que devam abranger vários usuários ou dispositivos, um banco de dado independente do servidor é quase certamente a melhor opção. As opções incluem:

* Banco de dados SQL relacional
* Repositório de chave-valor
* Repositório de BLOB
* Repositório de tabelas

Depois que os dados são salvos no banco de dado, um novo circuito pode ser iniciado por um usuário a qualquer momento. Os dados do usuário são mantidos e estão disponíveis em qualquer circuito novo.

Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte a [documentação do armazenamento do Azure](/azure/storage/) e [os bancos de dados do Azure](https://azure.microsoft.com/product-categories/databases/).

### <a name="url"></a>URL

Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL. Exemplos de estado modelado na URL incluem:

* A ID de uma entidade exibida.
* O número da página atual em uma grade paginável.

O conteúdo da barra de endereços do navegador é mantido:

* Se o usuário recarregar a página manualmente.
* Se o servidor Web ficar indisponível&mdash;o usuário será forçado a recarregar a página para se conectar a um servidor diferente.

Para obter informações sobre como definir padrões de URL com a diretiva `@page`, consulte <xref:blazor/routing>.

### <a name="client-side-in-the-browser"></a>Lado do cliente no navegador

Para dados transitórios que o usuário está criando ativamente, um repositório de backup comum é o `localStorage` do navegador e as coleções de `sessionStorage`. O aplicativo não é necessário para gerenciar ou limpar o estado armazenado se o circuito for abandonado, o que é uma vantagem sobre o armazenamento do lado do servidor.

> [!NOTE]
> "Lado do cliente" nesta seção refere-se aos cenários do lado do cliente no navegador, não ao [modelo de hospedagem do WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly). `localStorage` e `sessionStorage` podem ser usados em Blazor aplicativos Webassembly, mas apenas escrevendo código personalizado ou usando um pacote de terceiros.

`localStorage` e `sessionStorage` diferem da seguinte maneira:

* `localStorage` está no escopo do navegador do usuário. Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá. Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias. Os dados persistem em `localStorage` até serem explicitamente desmarcados.
* `sessionStorage` está no escopo da guia do navegador do usuário. Se o usuário recarregar a guia, o estado persiste. Se o usuário fechar a guia ou o navegador, o estado será perdido. Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.

Em geral, `sessionStorage` é mais seguro de usar. `sessionStorage` evita o risco de um usuário abrir várias guias e encontrar o seguinte:

* Bugs no armazenamento de estado entre guias.
* Comportamento confuso quando uma guia substitui o estado de outras guias.

`localStorage` é a melhor opção se o aplicativo precisar manter o estado entre fechar e reabrir o navegador.

Advertências para usar o armazenamento de navegador:

* Assim como o uso de um banco de dados do lado do servidor, o carregamento e o salvamento do data são assíncronos.
* Ao contrário de um banco de dados do lado do servidor, o armazenamento não está disponível durante o pré-processamento porque a página solicitada não existe no navegador durante o estágio de pré-processamento.
* O armazenamento de alguns kilobytes de dados é razoável para persistir para os aplicativos do Blazor Server. Além de alguns quilobytes, você deve considerar as implicações de desempenho porque os dados são carregados e salvos na rede.
* Os usuários podem exibir ou adulterar os dados. ASP.NET Core [proteção de dados](xref:security/data-protection/introduction) pode reduzir o risco.

## <a name="third-party-browser-storage-solutions"></a>Soluções de armazenamento de navegadores de terceiros

Os pacotes NuGet de terceiros fornecem APIs para trabalhar com `localStorage` e `sessionStorage`.

Vale a pena considerar a escolha de um pacote que usa de forma transparente a [proteção de dados](xref:security/data-protection/introduction)de ASP.NET Core. ASP.NET Core proteção de dados criptografa os dados armazenados e reduz o risco potencial de adulterar os dados armazenados. Se os dados serializados em JSON forem armazenados em texto não criptografado, os usuários poderão ver os dados usando as ferramentas de desenvolvedor do navegador e também modificar os dados armazenados. Proteger os dados nem sempre é um problema porque os dados podem ser triviais por natureza. Por exemplo, ler ou modificar a cor armazenada de um elemento de interface do usuário não é um risco de segurança significativo para o usuário ou a organização. Evite permitir que os usuários inspecionem ou violem *dados confidenciais*.

## <a name="protected-browser-storage-experimental-package"></a>Pacote experimental de armazenamento de navegador protegido

Um exemplo de um pacote NuGet que fornece [proteção de dados](xref:security/data-protection/introduction) para `localStorage` e `sessionStorage` é [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).

> [!WARNING]
> o `Microsoft.AspNetCore.ProtectedBrowserStorage` é um pacote experimental sem suporte não adequado para uso em produção no momento.

### <a name="installation"></a>Instalação

Para instalar o pacote de `Microsoft.AspNetCore.ProtectedBrowserStorage`:

1. No projeto de aplicativo Blazor Server, adicione uma referência de pacote a [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).
1. No HTML de nível superior (por exemplo, no arquivo *pages/_Host. cshtml* no modelo de projeto padrão), adicione a seguinte marca de `<script>`:

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. No método `Startup.ConfigureServices`, chame `AddProtectedBrowserStorage` para adicionar os serviços `localStorage` e `sessionStorage` à coleção de serviços:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Salvar e carregar dados dentro de um componente

Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) para injetar uma instância de um dos seguintes:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

A escolha depende de qual repositório de backup você deseja usar. No exemplo a seguir, `sessionStorage` é usado:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

A instrução `@using` pode ser colocada em um arquivo *_Imports. Razor* em vez de no componente. O uso do arquivo *_Imports. Razor* torna o namespace disponível para segmentos maiores do aplicativo ou todo o aplicativo.

Para manter o valor `currentCount` no componente `Counter` do modelo de projeto, modifique o método `IncrementCount` para usar `ProtectedSessionStore.SetAsync`:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável. Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo. `ProtectedSessionStore` serializa e desserializa automaticamente os dados JSON.

No exemplo de código anterior, os dados de `currentCount` são armazenados como `sessionStorage['count']` no navegador do usuário. Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando a [proteção de dados](xref:security/data-protection/introduction)do ASP.NET Core. Os dados criptografados podem ser vistos se `sessionStorage['count']` for avaliado no console do desenvolvedor do navegador.

Para recuperar os dados de `currentCount` se o usuário retornar ao componente `Counter` posteriormente (incluindo se eles estiverem em um circuito totalmente novo), use `ProtectedSessionStore.GetAsync`:

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua o resultado em `OnParametersSetAsync`, não `OnInitializedAsync`. `OnInitializedAsync` é chamado apenas uma vez quando o componente é instanciado primeiro. `OnInitializedAsync` não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página.

> [!WARNING]
> Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado. Com o pré-processamento habilitado, um erro é gerado semelhante a:
>
> > As chamadas de interoperabilidade do JavaScript não podem ser emitidas neste momento. Isso ocorre porque o componente está sendo renderizado.
>
> Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento. Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .

### <a name="handle-the-loading-state"></a>Manipular o estado de carregamento

Como o armazenamento de navegador é assíncrono (acessado através de uma conexão de rede), sempre há um período de tempo antes que os dados sejam carregados e estejam disponíveis para uso por um componente. Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.

Uma abordagem é controlar se os dados estão `null` (ainda carregando) ou não. No componente de `Counter` padrão, a contagem é mantida em um `int`. Torne `currentCount` anulável adicionando um ponto de interrogação (`?`) ao tipo (`int`):

```csharp
private int? currentCount;
```

Em vez de exibir incondicionalmente o botão contagem e **incremento** , escolha exibir esses elementos somente se os dados forem carregados:

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Processar pré-processamento

Durante o pré-processamento:

* Não existe uma conexão interativa com o navegador do usuário.
* O navegador ainda não tem uma página na qual possa executar código JavaScript.

`localStorage` ou `sessionStorage` não estão disponíveis durante o pré-processamento. Se o componente tentar interagir com o armazenamento, um erro será gerado semelhante a:

> As chamadas de interoperabilidade do JavaScript não podem ser emitidas neste momento. Isso ocorre porque o componente está sendo renderizado.

Uma maneira de resolver o erro é desabilitar o pré-processamento. Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador. O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil antes que `localStorage` ou `sessionStorage` estejam disponíveis.

::: moniker range=">= aspnetcore-3.1"

Para desabilitar o pré-processamento, abra o arquivo *pages/_Host. cshtml* e altere a chamada para `render-mode` do auxiliar de marca de `Component` para `Server`.

::: moniker-end

::: moniker range="< aspnetcore-3.1"

Para desabilitar o pré-processamento, abra o arquivo *pages/_Host. cshtml* e altere a chamada para `Html.RenderComponentAsync<App>(RenderMode.Server)`.

::: moniker-end

O pré-processamento pode ser útil para outras páginas que não usam `localStorage` ou `sessionStorage`. Para manter o pré-processamento habilitado, adie a operação de carregamento até que o navegador esteja conectado ao circuito. Veja a seguir um exemplo de como armazenar um valor de contador:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Fatorar a preservação do estado para um local comum

Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código. Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado. Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.

No exemplo a seguir de um componente `CounterStateProvider`, os dados do contador são persistidos:

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

O componente `CounterStateProvider` manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.

Para usar o componente `CounterStateProvider`, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador. Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o componente `CounterStateProvider` em volta do `Router` no componente `App` (*app. Razor*):

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Os componentes encapsulados recebem e podem modificar o estado do contador persistente. O componente `Counter` a seguir implementa o padrão:

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

O componente anterior não é necessário para interagir com `ProtectedBrowserStorage`, nem lidar com uma fase de "carregamento".

Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento. Consulte a seção [manipular pré-processamento](#handle-prerendering) para obter detalhes.

Em geral, o padrão de *componente pai do provedor de estado* é recomendado:

* Para consumir o estado em muitos outros componentes.
* Se houver apenas um objeto de estado de nível superior para persistir.

Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar manipular o carregamento e salvar o estado globalmente.
