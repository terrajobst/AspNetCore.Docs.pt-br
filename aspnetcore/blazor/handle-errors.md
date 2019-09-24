---
title: Tratar erros em aplicativos ASP.NET Core mais
author: guardrex
description: Descubra como ASP.NET Core mais incrivelmente o gerenciamento de exceções sem tratamento e como desenvolver aplicativos que detectem e lidem com erros.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/handle-errors
ms.openlocfilehash: de0a2f74df84f41581ac93dbeec7a5c5e90c6fa2
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207200"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Tratar erros em aplicativos ASP.NET Core mais

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

Este artigo descreve como o mais incrivelmente gerencia exceções sem tratamento e como desenvolver aplicativos que detectam e manipulam erros.

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a>Como a estrutura mais incrivelmente reage a exceções sem tratamento

O servidor mais incrivelmente é uma estrutura com estado. Enquanto os usuários interagem com um aplicativo, eles mantêm uma conexão com o servidor conhecido como um *circuito*. O circuito contém instâncias de componentes ativos, além de muitos outros aspectos do estado, como:

* A saída renderizada mais recente dos componentes.
* O conjunto atual de delegados de manipulação de eventos que poderia ser disparado por eventos do lado do cliente.

Se um usuário abrir o aplicativo em várias guias do navegador, ele terá vários circuitos independentes.

O mais grande trata as exceções não tratadas como fatais ao circuito onde elas ocorrem. Se um circuito for encerrado devido a uma exceção sem tratamento, o usuário só poderá continuar a interagir com o aplicativo recarregando a página para criar um novo circuito. Os circuitos fora do que foi encerrado, que são circuitos para outros usuários ou outras guias do navegador, não são afetados. Esse cenário é semelhante a um aplicativo de área de&mdash;trabalho que falha no aplicativo com falha deve ser reiniciado, mas outros aplicativos não são afetados.

Um circuito é encerrado quando ocorre uma exceção sem tratamento pelos seguintes motivos:

* Uma exceção sem tratamento geralmente deixa o circuito em um estado indefinido.
* A operação normal do aplicativo não pode ser garantida após uma exceção sem tratamento.
* As vulnerabilidades de segurança podem aparecer no aplicativo se o circuito continuar.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Gerenciar exceções sem tratamento no código do desenvolvedor

Para que um aplicativo continue após um erro, o aplicativo deve ter a lógica de tratamento de erros. As seções posteriores deste artigo descrevem possíveis fontes de exceções sem tratamento.

Em produção, não processe mensagens de exceção de estrutura ou rastreamentos de pilha na interface do usuário. O processamento de mensagens de exceção ou rastreamentos de pilha pode:

* Divulgue informações confidenciais para os usuários finais.
* Ajudar um usuário mal-intencionado a descobrir pontos fracos em um aplicativo que pode comprometer a segurança do aplicativo, do servidor ou da rede.

## <a name="log-errors-with-a-persistent-provider"></a>Registrar erros em log com um provedor persistente

Se ocorrer uma exceção sem tratamento, a exceção será registrada em <xref:Microsoft.Extensions.Logging.ILogger> instâncias configuradas no contêiner de serviço. Por padrão, os aplicativos mais incrivelmente registram em log a saída do console com o provedor de log do console. Considere fazer logon em um local mais permanente com um provedor que gerencia o tamanho do log e a rotação do log. Para obter mais informações, consulte <xref:fundamentals/logging/index>.

Durante o desenvolvimento, o mais claro geralmente envia os detalhes completos das exceções para o console do navegador para auxiliar na depuração. Na produção, os erros detalhados no console do navegador são desabilitados por padrão, o que significa que os erros não são enviados aos clientes, mas os detalhes completos da exceção ainda são registrados no lado do servidor. Para obter mais informações, consulte <xref:fundamentals/error-handling>.

Você deve decidir quais incidentes registrar e o nível de gravidade dos incidentes registrados. Usuários hostis podem ser capazes de disparar erros deliberadamente. Por exemplo, não registre um incidente de um erro em que um `ProductId` desconhecido é fornecido na URL de um componente que exibe detalhes do produto. Nem todos os erros devem ser tratados como incidentes de alta severidade para registro em log.

## <a name="places-where-errors-may-occur"></a>Locais onde podem ocorrer erros

O Framework e o código do aplicativo podem disparar exceções sem tratamento em qualquer um dos seguintes locais:

* [Instanciação de componente](#component-instantiation)
* [Métodos de ciclo de vida](#lifecycle-methods)
* [Lógica de renderização](#rendering-logic)
* [Manipuladores de eventos](#event-handlers)
* [Disposição do componente](#component-disposal)
* [Interoperabilidade de JavaScript](#javascript-interop)
* [Manipuladores de circuito](#circuit-handlers)
* [Disposição do circuito](#circuit-disposal)
* [Prerenderizando](#prerendering)

As exceções sem tratamento anteriores são descritas nas seções a seguir deste artigo.

### <a name="component-instantiation"></a>Instanciação de componente

Quando o mais claro cria uma instância de um componente:

* O construtor do componente é invocado.
* Os construtores de quaisquer serviços não singleton de di fornecidos ao construtor do componente por meio da [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) diretiva ou do atributo [[injetar]](xref:blazor/dependency-injection#request-a-service-in-a-component) são invocados. 

Um circuito falha quando qualquer Construtor executado ou um setter para qualquer `[Inject]` propriedade gera uma exceção sem tratamento. A exceção é fatal porque a estrutura não pode instanciar o componente. Se a lógica do Construtor puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.

### <a name="lifecycle-methods"></a>Métodos de ciclo de vida

Durante o tempo de vida de um componente, o mais claro invoca métodos de ciclo de vida:

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Se qualquer método de ciclo de vida lançar uma exceção, de forma síncrona ou assíncrona, a exceção será fatal para o circuito. Para componentes que lidam com erros em métodos de ciclo de vida, adicione a lógica de tratamento de erros.

No exemplo a seguir, `OnParametersSetAsync` onde chama um método para obter um produto:

* Uma exceção gerada no `ProductRepository.GetProductByIdAsync` método é manipulada por uma `try-catch` instrução.
* Quando o `catch` bloco é executado:
  * `loadFailed`é definido como `true`, que é usado para exibir uma mensagem de erro para o usuário.
  * O erro é registrado.

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Lógica de renderização

A marcação declarativa em um `.razor` arquivo de componente é compilada C# em um `BuildRenderTree`método chamado. Quando um componente renderiza, `BuildRenderTree` o executa e cria uma estrutura de dados que descreve os elementos, o texto e os componentes filho do componente renderizado.

A lógica de renderização pode gerar uma exceção. Um exemplo desse cenário ocorre quando `@someObject.PropertyName` o é avaliado, mas `null` `@someObject` é. Uma exceção sem tratamento gerada pela lógica de renderização é fatal para o circuito.

Para evitar uma exceção de referência nula na lógica de renderização, verifique `null` se há um objeto antes de acessar seus membros. No exemplo a seguir, `person.Address` as propriedades não são `person.Address` acessadas se for `null`:

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

O código anterior pressupõe que `person` não `null`é. Geralmente, a estrutura do código garante que um objeto exista no momento em que o componente é renderizado. Nesses casos, não é necessário verificar na lógica de `null` renderização. No exemplo anterior, `person` talvez seja garantido que exista porque `person` é criado quando o componente é instanciado.

### <a name="event-handlers"></a>Manipuladores de eventos

O código do lado do cliente dispara invocações C# de código quando manipuladores de eventos são criados usando:

* `@onclick`
* `@onchange`
* Outros `@on...` atributos
* `@bind`

O código do manipulador de eventos pode gerar uma exceção sem tratamento nesses cenários.

Se um manipulador de eventos lançar uma exceção sem tratamento (por exemplo, uma consulta de banco de dados falhar), a exceção será fatal para o circuito. Se o aplicativo chamar o código que poderia falhar por motivos externos, intercepte as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log.

Se o código do usuário não interceptar e manipular a exceção, a estrutura registrará a exceção e encerrará o circuito.

### <a name="component-disposal"></a>Disposição do componente

Um componente pode ser removido da interface do usuário, por exemplo, porque o usuário navegou para outra página. Quando um componente que implementa <xref:System.IDisposable?displayProperty=fullName> o é removido da interface do usuário, a estrutura chama o <xref:System.IDisposable.Dispose*> método do componente. 

Se o método do `Dispose` componente lançar uma exceção sem tratamento, a exceção será fatal para o circuito. Se a lógica de descarte puder gerar exceções, o aplicativo deverá interceptar as exceções usando uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.

Para obter mais informações sobre a disposição de <xref:blazor/components#component-disposal-with-idisposable>componentes, consulte.

### <a name="javascript-interop"></a>Interoperabilidade do JavaScript

`IJSRuntime.InvokeAsync<T>`permite que o código .NET faça chamadas assíncronas para o tempo de execução do JavaScript no navegador do usuário.

As seguintes condições se aplicam ao tratamento `InvokeAsync<T>`de erros com:

* Se uma chamada para `InvokeAsync<T>` falhar de forma síncrona, ocorrerá uma exceção do .net. Uma chamada para `InvokeAsync<T>` minha falha, por exemplo, porque os argumentos fornecidos não podem ser serializados. O código do desenvolvedor deve capturar a exceção. Se o código do aplicativo em um manipulador de eventos ou método de ciclo de vida do componente não tratar uma exceção, a exceção resultante será fatal para o circuito.
* Se uma chamada para `InvokeAsync<T>` falhar de forma assíncrona <xref:System.Threading.Tasks.Task> , o .net falhará. Uma chamada para `InvokeAsync<T>` pode falhar, por exemplo, porque o código do lado do JavaScript gera uma exceção ou retorna `Promise` um que foi `rejected`concluído como. O código do desenvolvedor deve capturar a exceção. Se estiver usando o operador [Await](/dotnet/csharp/language-reference/keywords/await) , considere encapsular a chamada de método em uma instrução [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log. Caso contrário, o código com falha resultará em uma exceção sem tratamento que é fatal para o circuito.
* Por padrão, as chamadas `InvokeAsync<T>` para devem ser concluídas em um determinado período ou a chamada atinge o tempo limite. O período de tempo limite padrão é de um minuto. O tempo limite protege o código contra uma perda na conectividade de rede ou no código JavaScript que nunca envia uma mensagem de conclusão. Se a chamada atingir o tempo limite, `Task` o resultado falhará com um. <xref:System.OperationCanceledException> Interceptar e processar a exceção com registro em log.

Da mesma forma, o código JavaScript pode iniciar chamadas para métodos .NET indicados pelo [atributo [JSInvokable]](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions). Se esses métodos .NET lançarem uma exceção sem tratamento:

* A exceção não é tratada como fatal para o circuito.
* O lado `Promise` do JavaScript é rejeitado.

Você tem a opção de usar o código de tratamento de erros no lado do .NET ou no lado do JavaScript da chamada do método.

Para obter mais informações, consulte <xref:blazor/javascript-interop>.

### <a name="circuit-handlers"></a>Manipuladores de circuito

O mais incrivelmente permite que o código defina um *manipulador de circuito*, que recebe notificações quando o estado do circuito de um usuário é alterado. Os seguintes Estados são usados:

* `initialized`
* `connected`
* `disconnected`
* `disposed`

As `CircuitHandler` notificações são gerenciadas pelo registro de um serviço di que herda da classe base abstrata.

Se os métodos de um manipulador de circuitos personalizados lançarem uma exceção sem tratamento, a exceção será fatal para o circuito. Para tolerar exceções no código de um manipulador ou em métodos chamados, empacote o código em uma ou mais instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com o tratamento de erros e o registro em log.

### <a name="circuit-disposal"></a>Disposição do circuito

Quando um circuito termina porque um usuário se desconectou e a estrutura está limpando o estado do circuito, a estrutura descarta o escopo de DI do circuito. Descartar o escopo descarta todos os serviços de DI no escopo do <xref:System.IDisposable?displayProperty=fullName>circuito que implementam o. Se qualquer serviço de DI lançar uma exceção sem tratamento durante a alienação, a estrutura registrará a exceção.

### <a name="prerendering"></a>Prerenderizando

Componentes mais flexíveis podem ser renderizados usando `Html.RenderComponentAsync` para que a marcação HTML renderizada seja retornada como parte da solicitação HTTP inicial do usuário. Isso funciona por:

* Criar um novo circuito que contém todos os componentes renderizados que fazem parte da mesma página.
* Gerando o HTML inicial.
* Tratar o circuito como `disconnected` até que o navegador do usuário estabeleça uma conexão de sinalização de volta ao mesmo servidor para retomar a interatividade no circuito.

Se qualquer componente lançar uma exceção sem tratamento durante o pré-processamento, por exemplo, durante um método de ciclo de vida ou na lógica de renderização:

* A exceção é fatal para o circuito.
* A exceção é lançada na pilha de chamadas da `Html.RenderComponentAsync` chamada. Portanto, toda a solicitação HTTP falha, a menos que a exceção seja explicitamente detectada pelo código do desenvolvedor.

Em circunstâncias normais, quando o pré-processamento falha, continuar a criar e renderizar o componente não faz sentido porque um componente de trabalho não pode ser renderizado.

Para tolerar erros que podem ocorrer durante o pré-processamento, a lógica de tratamento de erro deve ser colocada dentro de um componente que pode gerar exceções. Use instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) com tratamento de erros e registro em log. Em vez de encapsular a `RenderComponentAsync` chamada para `try-catch` em uma instrução, coloque a lógica de tratamento de erros `RenderComponentAsync`no componente renderizado pelo.

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="recursive-rendering"></a>Renderização recursiva

Os componentes podem ser aninhados recursivamente. Isso é útil para representar estruturas de dados recursivas. Por exemplo, um `TreeNode` componente pode renderizar `TreeNode` mais componentes para cada um dos filhos do nó.

Ao renderizar recursivamente, evite codificar padrões que resultam em recursão infinita:

* Não renderizar recursivamente uma estrutura de dados que contém um ciclo. Por exemplo, não renderize um nó de árvore cujos filhos incluam a si mesmo.
* Não crie uma cadeia de layouts que contenham um ciclo. Por exemplo, não crie um layout cujo layout seja próprio.
* Não permita que um usuário final viole invariáveis de recursão (regras) por meio de entradas de dados mal-intencionados ou chamadas de interoperabilidade do JavaScript.

Loops infinitos durante a renderização:

* Faz com que o processo de renderização continue para sempre.
* É equivalente a criar um loop não finalizado.

Nesses cenários, o circuito afetado trava e o thread geralmente tenta:

* Consuma tanto tempo de CPU quanto permitido pelo sistema operacional, indefinidamente.
* Consuma uma quantidade ilimitada de memória do servidor. O consumo de memória ilimitada é equivalente ao cenário em que um loop não finalizado adiciona entradas a uma coleção em cada iteração.

Para evitar padrões de recursão infinitos, verifique se o código de renderização recursivo contém condições de parada adequadas.

### <a name="custom-render-tree-logic"></a>Lógica de árvore de renderização personalizada

A maioria dos componentes mais podestas são implementados como arquivos *. Razor* e são compilados para `RenderTreeBuilder` produzir lógica que opera em um para renderizar sua saída. Um desenvolvedor pode implementar `RenderTreeBuilder` A lógica manualmente usando o código de procedimento. C# Para obter mais informações, consulte <xref:blazor/components#manual-rendertreebuilder-logic>.

> [!WARNING]
> O uso da lógica do construtor de árvore de renderização manual é considerado um cenário avançado e não seguro, não recomendado para o desenvolvimento geral de componentes.

Se `RenderTreeBuilder` o código for escrito, o desenvolvedor deverá garantir a exatidão do código. Por exemplo, o desenvolvedor deve garantir que:

* Chamadas para `OpenElement` e `CloseElement` são balanceadas corretamente.
* Os atributos são adicionados somente nos locais corretos.

A lógica do construtor de árvore de renderização manual incorreta pode causar um comportamento indefinido arbitrário, incluindo falhas, travamentos do servidor e vulnerabilidades de segurança.

Considere a lógica do construtor de árvore de renderização manual no mesmo nível de complexidade e com o mesmo nível de *perigo* de escrever código de assembly ou instruções MSIL manualmente.
