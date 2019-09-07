---
title: Proteger ASP.NET Core aplicativos mais fáceis no servidor
author: guardrex
description: Saiba como mitigar as ameaças de segurança para aplicativos mais fáceis no servidor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/blazor/server-side
ms.openlocfilehash: 13bb4475b4beac78cf489d83fb59a3e0d6d8f2d9
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800500"
---
# <a name="secure-aspnet-core-blazor-server-side-apps"></a>Proteger ASP.NET Core aplicativos mais fáceis no servidor

Por [Javier Calvarro Nelson](https://github.com/javiercn)

Os aplicativos mais novos do lado do servidor adotam um modelo de processamento de dados com *estado* , onde o servidor e o cliente mantêm uma relação de vida longa. O estado persistente é mantido por um [circuito](xref:blazor/state-management), que pode abranger conexões que também são potencialmente de longa duração.

Quando um usuário visita um site do lado do servidor mais incrivelmente, o servidor cria um circuito na memória do servidor. O circuito indica ao navegador qual conteúdo renderizar e responde a eventos, como quando o usuário seleciona um botão na interface do usuário. Para executar essas ações, um circuito invoca funções JavaScript no navegador do usuário e nos métodos do .NET no servidor. Essa interação baseada em JavaScript bidirecional é conhecida como interoperabilidade [JavaScript (JS Interop)](xref:blazor/javascript-interop).

Como a interoperabilidade do JS ocorre pela Internet e o cliente usa um navegador remoto, aplicativos mais pousados no servidor compartilham a maioria das preocupações com segurança de aplicativos Web. Este tópico descreve as ameaças comuns a aplicativos de mais alto do servidor e fornece diretrizes de mitigação de ameaças voltadas para aplicativos voltados para a Internet.

Em ambientes restritos, como dentro de redes corporativas ou intranets, algumas das diretrizes de mitigação:

* Não se aplica ao ambiente restrito.
* Não vale o custo para implementar porque o risco de segurança está baixo em um ambiente restrito.

## <a name="resource-exhaustion"></a>Esgotamento de recursos

O esgotamento de recursos pode ocorrer quando um cliente interage com o servidor e faz com que o servidor consuma recursos excessivos. O consumo excessivo de recursos afeta principalmente:

* [CPU](#cpu)
* [Memória](#memory)
* [Conexões de cliente](#client-connections)

Ataques de DoS (negação de serviço) geralmente buscam esgotar os recursos de um aplicativo ou servidor. No entanto, o esgotamento de recursos não é necessariamente o resultado de um ataque no sistema. Por exemplo, recursos finitos podem ser esgotados devido à alta demanda do usuário. O DoS é abordado mais detalhadamente na seção [ataques dos (negação de serviço)](#denial-of-service-dos-attacks) .

Recursos externos à estrutura mais experiente, como bancos de dados e identificadores de arquivos (usados para ler e gravar arquivos), também podem ter esgotamento de recursos. Para obter mais informações, consulte <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

O esgotamento de CPU pode ocorrer quando um ou mais clientes forçam o servidor a executar um trabalho intensivo de CPU.

Por exemplo, considere um aplicativo do lado do servidor mais incrivelmente que calcule um *número Fibonnacci*. Um número Fibonnacci é produzido de uma sequência Fibonnacci, em que cada número na sequência é a soma dos dois números anteriores. A quantidade de trabalho necessária para alcançar a resposta depende do comprimento da sequência e do tamanho do valor inicial. Se o aplicativo não coloca limites na solicitação de um cliente, os cálculos com uso intensivo de CPU podem dominar o tempo da CPU e diminuir o desempenho de outras tarefas. O consumo excessivo de recursos é uma preocupação de segurança que afeta a disponibilidade.

O esgotamento da CPU é uma preocupação para todos os aplicativos voltados ao público. Em aplicativos Web regulares, solicitações e conexões atingiram o tempo limite como uma proteção, mas aplicativos do lado do servidor mais incrivelmente não fornecem as mesmas proteções. Aplicativos mais úteis do lado do servidor devem incluir verificações e limites apropriados antes de executar um trabalho potencialmente intensivo de CPU.

### <a name="memory"></a>Memória

O esgotamento de memória pode ocorrer quando um ou mais clientes forçam o servidor a consumir uma grande quantidade de memória.

Por exemplo, considere um aplicativo do lado do servidor mais incrivelmente com um componente que aceita e exibe uma lista de itens. Se o aplicativo mais grande não coloca os limites no número de itens permitidos ou no número de itens processados de volta para o cliente, o processamento e a renderização com uso intensivo de memória podem dominar a memória do servidor até o ponto em que o desempenho do servidor sofre. O servidor pode falhar ou ficar lento até o ponto em que parece ter falhado.

Considere o cenário a seguir para manter e exibir uma lista de itens que pertencem a um possível cenário de esgotamento de memória no servidor:

* Os itens em uma `List<MyItem>` propriedade ou campo usam a memória do servidor. Se o aplicativo permitir que a lista de itens cresça sem limites, haverá um risco de que o servidor fique com memória insuficiente. A execução de memória insuficiente faz com que a sessão atual seja encerrada (falha) e todas as sessões simultâneas nessa instância do servidor recebam uma exceção de memória insuficiente. Para evitar que esse cenário ocorra, o aplicativo deve usar uma estrutura de dados que impõe um limite de item em usuários simultâneos.
* Se um esquema de paginação não for usado para renderização, o servidor usará memória adicional para objetos que não estão visíveis na interface do usuário. Sem um limite para o número de itens, as demandas de memória podem esgotar a memória disponível do servidor. Para evitar esse cenário, use uma das seguintes abordagens:
  * Use listas paginadas ao renderizar.
  * Exiba apenas os primeiros 100 a 1.000 itens e exija que o usuário insira critérios de pesquisa para localizar itens além dos itens exibidos.
  * Para um cenário de renderização mais avançado, implemente listas ou grades que dão suporte à *virtualização*. Usando a virtualização, as listas só renderizam um subconjunto de itens visíveis no momento para o usuário. Quando o usuário interage com a barra de rolagem na interface do usuário, o componente renderiza somente os itens necessários para exibição. Os itens que atualmente não são necessários para exibição podem ser mantidos no armazenamento secundário, que é a abordagem ideal. Itens não exibidos também podem ser mantidos na memória, o que é menos ideal.

Os aplicativos mais polado no servidor oferecem um modelo de programação semelhante a outras estruturas de interface do usuário para aplicativos com estado, como WPF, Windows Forms ou mais de um lado do cliente. A principal diferença é que, em várias das estruturas da interface do usuário, a memória consumida pelo aplicativo pertence ao cliente e afeta apenas esse cliente individual. Por exemplo, um aplicativo do lado do cliente mais incrivelmente executado inteiramente no cliente e usa apenas recursos de memória do cliente. No cenário mais alto do servidor, a memória consumida pelo aplicativo pertence ao servidor e é compartilhada entre clientes na instância do servidor.

As demandas de memória do lado do servidor são uma consideração para todos os aplicativos do lado do servidor. No entanto, a maioria dos aplicativos Web não tem estado e a memória usada durante o processamento de uma solicitação é liberada quando a resposta é retornada. Como recomendação geral, não permita que os clientes aloquem uma quantidade não associada de memória como em qualquer outro aplicativo do lado do servidor que persista as conexões do cliente. A memória consumida por um aplicativo mais útil no lado do servidor persiste por mais tempo do que uma única solicitação.

> [!NOTE]
> Durante o desenvolvimento, um criador de perfil pode ser usado ou um rastreamento capturado para avaliar as demandas de memória dos clientes. Um criador de perfil ou rastreamento não capturará a memória alocada para um cliente específico. Para capturar o uso de memória de um cliente específico durante o desenvolvimento, Capture um despejo e examine a demanda de memória de todos os objetos com raiz no circuito do usuário.

### <a name="client-connections"></a>Conexões de cliente

O esgotamento de conexão pode ocorrer quando um ou mais clientes abrem muitas conexões simultâneas com o servidor, impedindo que outros clientes estabeleçam novas conexões.

Clientes mais grandes estabelecem uma única conexão por sessão e mantêm a conexão aberta enquanto a janela do navegador está aberta. As demandas no servidor de manutenção de todas as conexões não são específicas para aplicativos mais Incrivelmenteos. Considerando a natureza persistente das conexões e a natureza com estado dos aplicativos mais monitorados no lado do servidor, o esgotamento da conexão é um risco maior à disponibilidade do aplicativo.

Por padrão, não há limite para o número de conexões por usuário para um aplicativo mais alto no lado do servidor. Se o aplicativo exigir um limite de conexão, execute uma ou mais das seguintes abordagens:

* Exigir autenticação, o que limita naturalmente a capacidade de usuários não autorizados se conectarem ao aplicativo. Para que esse cenário seja eficaz, os usuários devem ser impedidos de provisionar novos usuários no.
* Limite o número de conexões por usuário. A limitação de conexões pode ser realizada por meio das abordagens a seguir. Tome cuidado para permitir que usuários legítimos acessem o aplicativo (por exemplo, quando um limite de conexão é estabelecido com base no endereço IP do cliente).
  * No nível do aplicativo:
    * Extensibilidade de roteamento de ponto de extremidade.
    * Exigir autenticação para se conectar ao aplicativo e controlar as sessões ativas por usuário.
    * Rejeite novas sessões após atingir um limite.
    * Conexões de WebSocket de proxy para um aplicativo por meio do uso de um proxy, como o [serviço de signalr do Azure](/azure/azure-signalr/signalr-overview) que multiplexa conexões de clientes para um aplicativo. Isso fornece um aplicativo com maior capacidade de conexão do que um único cliente pode estabelecer, impedindo que um cliente esgotasse as conexões com o servidor.
  * No nível do servidor: Use um proxy/gateway na frente do aplicativo. Por exemplo, a [porta frontal do Azure](/azure/frontdoor/front-door-overview) permite que você defina, gerencie e monitore o roteamento global do tráfego da Web para um aplicativo.

## <a name="denial-of-service-dos-attacks"></a>Ataques de negação de serviço (DoS)

Ataques de DoS (negação de serviço) envolvem um cliente que está fazendo com que o servidor esgotasse um ou mais de seus recursos, tornando o aplicativo indisponível. Os aplicativos mais no lado do servidor incluem alguns limites padrão e dependem de outros ASP.NET Core e limites de sinalização para proteger contra ataques dos:

| Limite de aplicativo mais alto do servidor                            | Descrição | Padrão |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Número máximo de circuitos desconectados que um determinado servidor mantém na memória por vez. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Quantidade máxima de tempo que um circuito desconectado é mantido na memória antes de ser interrompido. | 3 minutos |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Quantidade máxima de tempo que o servidor espera antes de atingir o tempo limite de uma invocação de função JavaScript assíncrona. | 1 minuto |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Número máximo de lotes de renderização não confirmados que o servidor mantém na memória por circuito em um determinado momento para dar suporte à reconexão robusta. Depois de atingir o limite, o servidor para de produzir novos lotes de renderização até que um ou mais lotes tenham sido confirmados pelo cliente. | 10 |


| Limite de sinalização e ASP.NET Core             | Descrição | Padrão |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Tamanho da mensagem para uma mensagem individual. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interações com o navegador (cliente)

Um cliente interage com o servidor por meio de expedição de evento de interoperabilidade do JS e conclusão do processamento. A comunicação de interoperabilidade do JS é de ambas as maneiras entre JavaScript e .NET:

* Os eventos de navegador são expedidos do cliente para o servidor de maneira assíncrona.
* O servidor responde de forma assíncrona reprocessando a interface do usuário conforme necessário.

### <a name="javascript-functions-invoked-from-net"></a>Funções JavaScript invocadas do .NET

Para chamadas de métodos .NET para JavaScript:

* Todas as invocações têm um tempo limite configurável após o qual falham, <xref:System.OperationCanceledException> retornando um para o chamador.
  * Há um tempo limite padrão para as chamadas (`CircuitOptions.JSInteropDefaultCallTimeout`) de um minuto.
  * Um token de cancelamento pode ser fornecido para controlar o cancelamento em uma base por chamada. Conte com o tempo limite de chamada padrão quando possível e Associação de tempo qualquer chamada ao cliente se um token de cancelamento for fornecido.
* O resultado de uma chamada JavaScript não pode ser confiável. O cliente de aplicativo mais incrivelmente executado no navegador pesquisa a função JavaScript a ser invocada. A função é invocada e o resultado ou um erro é produzido. Um cliente mal-intencionado pode tentar:
  * Causar um problema no aplicativo retornando um erro da função JavaScript.
  * Induzir um comportamento indesejado no servidor retornando um resultado inesperado da função JavaScript.

Tome as seguintes precauções para proteger contra os cenários anteriores:

* Encapsular chamadas de interoperabilidade JS em instruções [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) para considerar erros que podem ocorrer durante as invocações. Para obter mais informações, consulte <xref:blazor/handle-errors#javascript-interop>.
* Valide os dados retornados das invocações de interoperabilidade do JS, incluindo mensagens de erro, antes de realizar qualquer ação.

### <a name="net-methods-invoked-from-the-browser"></a>Métodos .NET invocados no navegador

Não confie em chamadas de JavaScript para métodos .NET. Quando um método .NET é exposto ao JavaScript, considere como o método .NET é invocado:

* Trate qualquer método .NET exposto ao JavaScript como você faria com um ponto de extremidade público para o aplicativo.
  * Validar entrada.
    * Verifique se os valores estão dentro dos intervalos esperados.
    * Verifique se o usuário tem permissão para executar a ação solicitada.
  * Não aloque uma quantidade excessiva de recursos como parte da invocação do método .NET. Por exemplo, execute verificações e coloque limites no uso de CPU e memória.
  * Leve em consideração que os métodos estáticos e de instância podem ser expostos a clientes JavaScript. Evite compartilhar o estado entre as sessões, a menos que o design chame o estado de compartilhamento com as restrições apropriadas.
    * Para métodos de instância expostos `DotNetReference` por meio de objetos criados originalmente por injeção de dependência (di), os objetos devem ser registrados como objetos com escopo. Isso se aplica a qualquer serviço de DI que o aplicativo do lado do servidor mais incrivelmente usa.
    * Para métodos estáticos, evite estabelecer o estado que não pode ser definido como escopo para o cliente, a menos que o aplicativo esteja explicitamente compartilhando o estado por design em todos os usuários em uma instância de servidor.
  * Evite passar dados fornecidos pelo usuário em parâmetros para chamadas JavaScript. Se a passagem de dados em parâmetros for absolutamente necessária, verifique se o código JavaScript lida com a passagem dos dados sem introduzir vulnerabilidades de [XSS (script entre sites)](#cross-site-scripting-xss) . Por exemplo, não grave dados fornecidos pelo usuário no modelo de objeto do documento (dom) definindo a `innerHTML` propriedade de um elemento. Considere o uso da [política de segurança de conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para desabilitar `eval` e outros primitivos JavaScript não seguros.
* Evite implementar a expedição personalizada de invocações do .NET sobre a implementação de expedição da estrutura. Expor métodos .NET ao navegador é um cenário avançado, não recomendado para desenvolvimento geral mais incrivelmente.

### <a name="events"></a>Events

Os eventos fornecem um ponto de entrada para um aplicativo do lado do servidor mais incrivelmente. As mesmas regras para proteger pontos de extremidade em aplicativos Web se aplicam à manipulação de eventos no mais alto aplicativo do lado do servidor. Um cliente mal-intencionado pode enviar todos os dados que deseja enviar como a carga de um evento.

Por exemplo:

* Um evento de alteração para `<select>` um pode enviar um valor que não está dentro das opções que o aplicativo apresentou ao cliente.
* Um `<input>` pode enviar qualquer dado de texto para o servidor, ignorando a validação do lado do cliente.

O aplicativo deve validar os dados para qualquer evento que o aplicativo manipular. Os componentes do mais simples Framework [Forms](xref:blazor/forms-validation) executam validações básicas. Se o aplicativo usar componentes de formulários personalizados, o código personalizado deverá ser escrito para validar os dados de evento conforme apropriado.

Os eventos mais novos do lado do servidor são assíncronos, de modo que vários eventos podem ser expedidos para o servidor antes que o aplicativo tenha tempo de reagir, produzindo um novo processamento. Isso tem algumas implicações de segurança a serem consideradas. A limitação de ações do cliente no aplicativo deve ser executada dentro de manipuladores de eventos e não depende do estado atual da exibição renderizada.

Considere um componente de contador que deve permitir que um usuário aumente um contador no máximo três vezes. O botão para incrementar o contador é condicionalmente baseado no valor de `count`:

```cshtml
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Um cliente pode enviar um ou mais eventos de incremento antes que a estrutura produza um novo processamento desse componente. O resultado é que o `count` pode ser incrementado *três vezes* pelo usuário, pois o botão não é removido pela interface de usuário com rapidez suficiente. A maneira correta de atingir o limite de três `count` incrementos é mostrada no exemplo a seguir:

```cshtml
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Ao adicionar a `if (count < 3) { ... }` verificação dentro do manipulador, a decisão de incrementar `count` é baseada no estado atual do aplicativo. A decisão não se baseia no estado da interface do usuário como estava no exemplo anterior, que pode estar temporariamente obsoleta.

## <a name="additional-security-guidance"></a>Diretrizes de segurança adicionais

As diretrizes para proteger os aplicativos ASP.NET Core se aplicam a aplicativos mais pousados no lado do servidor e são abordadas nas seguintes seções:

* [Registro em log e dados confidenciais](#logging-and-sensitive-data)
* [Proteger informações em trânsito com HTTPS](#protect-information-in-transit-with-https)
* [Scripts entre sites (XSS)](#cross-site-scripting-xss))
* [Proteção entre origens](#cross-origin-protection)
* [Sensor de clique](#click-jacking)
* [Redirecionamentos abertos](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Registro em log e dados confidenciais

As interações de interoperabilidade do js entre o cliente e o servidor são <xref:Microsoft.Extensions.Logging.ILogger> registradas nos logs do servidor com instâncias. O mais incrivelmente evita o registro de informações confidenciais, como eventos reais ou entradas e saídas de interoperabilidade do JS.

Quando ocorre um erro no servidor, a estrutura notifica o cliente e destrói a sessão. Por padrão, o cliente recebe uma mensagem de erro genérica que pode ser vista nas ferramentas de desenvolvedor do navegador.

O erro do lado do cliente não inclui a pilha de chamadas e não fornece detalhes sobre a causa do erro, mas os logs do servidor contêm essas informações. Para fins de desenvolvimento, informações de erro confidenciais podem ser disponibilizadas para o cliente habilitando erros detalhados.

Habilitar erros detalhados com:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`chave de configuração. Por exemplo, defina a `ASPNETCORE_DETAILEDERRORS` variável de ambiente como um valor `true`de.

> [!WARNING]
> Expor informações de erro aos clientes na Internet é um risco de segurança que sempre deve ser evitado.

### <a name="protect-information-in-transit-with-https"></a>Proteger informações em trânsito com HTTPS

O lado do servidor mais incrivelmente usa o Signalr para comunicação entre o cliente e o servidor. O lado do servidor mais alto normalmente usa o transporte que o Signalr negocia, que normalmente é WebSockets.

O lado do servidor mais incrivelmente não garante a integridade e a confidencialidade dos dados enviados entre o servidor e o cliente. Sempre use HTTPS.

### <a name="cross-site-scripting-xss"></a>Scripts entre sites (XSS)

O XSS (script entre sites) permite que uma parte não autorizada execute lógica arbitrária no contexto do navegador. Um aplicativo comprometido poderia potencialmente executar código arbitrário no cliente. A vulnerabilidade pode ser usada para potencialmente executar várias ações mal-intencionadas no servidor:

* Despache eventos falsos/inválidos para o servidor.
* Falha de expedição/conclusões de renderização inválidas.
* Evite a expedição de conclusões de renderização.
* Despache chamadas de interoperabilidade do JavaScript para o .NET.
* Modifique a resposta das chamadas de interoperabilidade do .NET para o JavaScript.
* Evite distribuir resultados de interoperabilidade do .NET para JS.

A estrutura do lado do servidor do mais Altova as etapas para se proteger contra algumas das ameaças anteriores:

* Interromperá a produção de novas atualizações de interface do usuário se o cliente não estiver confirmando lotes de renderização. Configurado com `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.
* O tempo limite de qualquer chamada .NET para JavaScript após um minuto sem receber uma resposta do cliente. Configurado com `CircuitOptions.JSInteropDefaultCallTimeout`.
* Executa a validação básica em todas as entradas provenientes do navegador durante a interoperabilidade do JS:
  * As referências do .NET são válidas e do tipo esperado pelo método .NET.
  * Os dados não estão malformados.
  * O número correto de argumentos para o método está presente na carga.
  * Os argumentos ou o resultado podem ser desserializados corretamente antes de invocar o método.
* Executa a validação básica em todas as entradas provenientes do navegador de eventos expedidos:
  * O evento tem um tipo válido.
  * Os dados do evento podem ser desserializados.
  * Há um manipulador de eventos associado ao evento.

Além das proteções que a estrutura implementa, o aplicativo deve ser codificado pelo desenvolvedor para proteger contra ameaças e tomar as medidas apropriadas:

* Sempre validar dados ao manipular eventos.
* Execute a ação apropriada ao receber dados inválidos:
  * Ignorar os dados e retornar. Isso permite que o aplicativo continue processando solicitações.
  * Se o aplicativo determinar que a entrada é ilegítimo e não pôde ser produzida pelo cliente legítimo, acione uma exceção. Lançar uma exceção destrói o circuito e encerra a sessão.
* Não confie na mensagem de erro fornecida por conclusões de lote de renderização incluídas nos logs. O erro é fornecido pelo cliente e, em geral, não pode ser confiável, pois o cliente pode estar comprometido.
* Não confie na entrada em chamadas de interoperabilidade JS em qualquer direção entre os métodos JavaScript e .NET.
* O aplicativo é responsável por validar que o conteúdo dos argumentos e dos resultados é válido, mesmo que os argumentos ou os resultados sejam desserializados corretamente.

Para que uma vulnerabilidade de XSS exista, o aplicativo deve incorporar a entrada do usuário na página renderizada. Os componentes mais polado do servidor executam uma etapa de tempo de compilação em que a marcação em um arquivo *. Razor* é transformada em lógica de procedimento C# . Em tempo de execução C# , a lógica cria uma *árvore de renderização* que descreve os elementos, o texto e os componentes filho. Isso é aplicado ao DOM do navegador por meio de uma sequência de instruções de JavaScript (ou é serializado para HTML no caso de pré-processamento):

* A entrada do usuário processada por meio de sintaxe Razor `@someStringValue`normal (por exemplo,) não expõe uma vulnerabilidade de XSS porque a sintaxe Razor é adicionada ao dom por meio de comandos que só podem gravar texto. Mesmo se o valor incluir marcação HTML, o valor será exibido como texto estático. Ao renderizar, a saída é codificada em HTML, que também exibe o conteúdo como texto estático.
* Marcas de script não são permitidas e não devem ser incluídas na árvore de renderização de componente do aplicativo. Se uma marca de script for incluída na marcação de um componente, um erro de tempo de compilação será gerado.
* Os autores de componentes podem criar C# componentes no sem usar o Razor. O autor do componente é responsável por usar as APIs corretas ao emitir a saída. Por exemplo, use `builder.AddContent(0, someUserSuppliedString)` e *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, pois o último pode criar uma vulnerabilidade de XSS.

Como parte da proteção contra ataques XSS, considere a implementação de mitigações XSS, como a [política de segurança de conteúdo (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Para obter mais informações, consulte <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Proteção entre origens

Os ataques entre origens envolvem um cliente de uma origem diferente executando uma ação no servidor. A ação mal-intencionada é normalmente uma solicitação GET ou uma POSTAgem de formulário (solicitação entre sites forjada, CSRF), mas também é possível abrir um WebSocket mal-intencionado. Os aplicativos mais polado no servidor oferecem [as mesmas garantias de que qualquer outro aplicativo de sinalização usando a oferta de protocolo de Hub](xref:signalr/security):

* Aplicativos mais novos no servidor podem ser acessados entre origens, a menos que sejam tomadas medidas adicionais para impedi-lo. Para desabilitar o acesso entre origens, desabilite o CORS no ponto de extremidade adicionando o middleware do CORS ao pipeline e adicionando o `DisableCorsAttribute` aos metadados do ponto de extremidade mais alto ou limite o conjunto de origens permitidas [Configurando o signalr para recursos entre origens compartilhamento](xref:signalr/security#cross-origin-resource-sharing).
* Se o CORS estiver habilitado, etapas adicionais poderão ser necessárias para proteger o aplicativo dependendo da configuração do CORS. Se o CORS for habilitado globalmente, o CORS poderá ser desabilitado para o Hub do lado do servidor mais `DisableCorsAttribute` incrivelmente adicionando os metadados aos metadados do `hub.MapBlazorHub()`ponto de extremidade após a chamada.

Para obter mais informações, consulte <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Sensor de clique

A tomada de clique envolve a renderização de um site `<iframe>` como um dentro de um site de uma origem diferente para induzir o usuário a executar ações no site sob ataque.

Para proteger um aplicativo contra a renderização dentro de `<iframe>`um, use o [CSP (política de segurança de conteúdo)](https://developer.mozilla.org/docs/Web/HTTP/CSP) e o `X-Frame-Options` cabeçalho. Para obter mais informações, [consulte MDN Web docs: X-frame-opções](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Redirecionamentos abertos

Quando uma sessão de aplicativo do lado do servidor mais incrivelmente inicia, o servidor executa a validação básica das URLs enviadas como parte do início da sessão. A estrutura verifica se a URL base é um pai da URL atual antes de estabelecer o circuito. Nenhuma verificação adicional é executada pela estrutura.

Quando um usuário seleciona um link no cliente, a URL do link é enviada para o servidor, que determina a ação a ser tomada. Por exemplo, o aplicativo pode executar uma navegação no lado do cliente ou indicar ao navegador para ir para o novo local.

Os componentes também podem disparar solicitações de navegação programaticamente `NavigationManager`por meio do uso do. Nesses cenários, o aplicativo pode executar uma navegação no lado do cliente ou indicar ao navegador para ir para o novo local.

Os componentes devem:

* Evite usar a entrada do usuário como parte dos argumentos de chamada de navegação.
* Valide os argumentos para garantir que o destino seja permitido pelo aplicativo.

Caso contrário, um usuário mal-intencionado pode forçar o navegador a ir para um site controlado pelo invasor. Nesse cenário, o invasor vaza o aplicativo para usar alguma entrada do usuário como parte da invocação do `NavigationManager.Navigate` método.

Esse aviso também se aplica ao renderizar links como parte do aplicativo:

* Se possível, use links relativos.
* Valide se os destinos de link absoluto são válidos antes de incluí-los em uma página.

Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Para obter orientação sobre autenticação e autorização, <xref:security/blazor/index>consulte.

## <a name="security-checklist"></a>Lista de verificação de segurança

A seguinte lista de considerações de segurança não é abrangente:

* Validar argumentos de eventos.
* Validar entradas e resultados de chamadas de interoperabilidade JS.
* Evite usar (ou validar com antecedência) a entrada do usuário para chamadas de interoperabilidade .NET para JS.
* Impedir que o cliente aloque uma quantidade desassociada de memória.
  * Dados dentro do componente.
  * `DotNetObject`referências retornadas ao cliente.
* Evite usar a entrada do usuário como parte das `NavigationManager.Navigate` chamadas para e validar a entrada do usuário para URLs em um conjunto de origens permitidas primeiro se for inevitável.
* Não tome decisões de autorização com base no estado da interface do usuário, mas somente no estado do componente.
* Considere o uso da [CSP (política de segurança de conteúdo)](https://developer.mozilla.org/docs/Web/HTTP/CSP) para proteger contra ataques de XSS.
* Considere usar o CSP e [as opções X-frame](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) para proteger contra o clique.
* Verifique se as configurações de CORS são apropriadas ao habilitar o CORS ou desabilitar explicitamente o CORS para aplicativos mais Incrivelmenteos.
* Teste para garantir que os limites do lado do servidor para o aplicativo mais experiente forneçam uma experiência de usuário aceitável sem níveis inaceitáveis de risco.
