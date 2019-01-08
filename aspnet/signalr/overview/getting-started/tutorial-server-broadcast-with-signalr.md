---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: 'Tutorial: Transmissão de servidor com SignalR 2 | Microsoft Docs'
author: tdykstra
description: Este tutorial mostra como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer funcionalidade de difusão de servidor.
ms.author: riande
ms.date: 01/02/2019
ms.topic: tutorial
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a6014e604613492db91b2dc6f846c3c73d938d99
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099293"
---
# <a name="tutorial-server-broadcast-with-signalr-2"></a>Tutorial: Servidor de transmissão com SignalR 2

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Este tutorial mostra como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer funcionalidade de difusão de servidor. Transmissão de servidor significa que o servidor inicia as comunicações enviadas para clientes.

O aplicativo que você criará neste tutorial simula uma bolsa, um cenário típico para a funcionalidade de difusão de servidor. Periodicamente, o servidor aleatoriamente de atualizações de preços de ações e as atualizações de difusão para todos os clientes conectados. No navegador, os números e símbolos na **alterar** e **%** colunas alteram dinamicamente em resposta a notificações do servidor. Se você abrir navegadores adicionais para a mesma URL, todos eles mostram os mesmos dados e as mesmas alterações aos dados simultaneamente.

![Criar web](tutorial-server-broadcast-with-signalr/_static/image1.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Criar o projeto
> * Configure o código de servidor
> * Examinar o código de servidor
> * Configure o código de cliente
> * Examinar o código do cliente
> * Testar o aplicativo
> * Habilite o registro em logs

> [!IMPORTANT]
> Se você não quiser trabalhar com as etapas de criação do aplicativo, você pode instalar o pacote SignalR.Sample em um novo projeto de aplicativo Web ASP.NET vazio. Se você instalar o pacote do NuGet sem executar as etapas neste tutorial, você deve seguir as instruções de *Readme. txt* arquivo. Para executar o pacote que você precisa adicionar uma inicialização do OWIN de classe que chama o `ConfigureSignalR` método no pacote instalado. Você receberá um erro se você não adicionar a classe de inicialização do OWIN. Consulte a [instalar o exemplo StockTicker](#install-the-stockticker-sample) seção deste artigo.


## <a name="prerequisites"></a>Pré-requisitos

 * [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) com o **ASP.NET e desenvolvimento web** carga de trabalho.

## <a name="create-the-project"></a>Criar o projeto

Esta seção mostra como usar o Visual Studio 2017 para criar um aplicativo de Web ASP.NET vazio.

1. No Visual Studio, crie um aplicativo Web ASP.NET.

    ![Criar web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. No **novo aplicativo de Web do ASP.NET - SignalR.StockTicker** janela, deixe **vazia** selecionado e selecione **Okey**.

## <a name="set-up-the-server-code"></a>Configure o código de servidor

Nesta seção, você deve definir o código que é executado no servidor.

### <a name="create-the-stock-class"></a>Criar a classe de estoque

Você começa criando a *Stock* classe que você usará para armazenar e transmitir informações sobre uma ação de modelo.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **classe**.

1. Nomeie a classe *Stock* e adicioná-lo ao projeto.

1. Substitua o código na *Stock.cs* arquivo com este código:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    São as duas propriedades que você definirá quando você cria stocks `Symbol` (por exemplo, MSFT da Microsoft) e `Price`. As outras propriedades dependem de como e quando você definir `Price`. Na primeira vez em que você definir `Price`, o valor é propagado para `DayOpen`. Depois disso, quando você define `Price`, o aplicativo calcula os `Change` e `PercentChange` valores de propriedade com base na diferença entre `Price` e `DayOpen`.

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a>Criar as classes StockTickerHub e StockTicker

Você usará a API de Hub do SignalR para manipular a interação do servidor para cliente. Um `StockTickerHub` classe que deriva de `SignalRHub` classe manipulará receber chamadas de método e de conexões de clientes. Você também precisa manter dados de estoque e executar um `Timer` objeto. O `Timer` objeto periodicamente irá disparar atualizações de preço independente de conexões de cliente. Você não pode colocar essas funções em um `Hub` de classe, como os Hubs são transitórios. O aplicativo cria um `Hub` instância da classe para cada tarefa no hub, como conexões e chamadas do cliente para o servidor. Portanto, o mecanismo que mantém os dados de estoque, atualiza os preços e transmite as atualizações de preço deve ser executado em uma classe separada. Você nomeará a classe `StockTicker`.

![Transmitindo de StockTicker](tutorial-server-broadcast-with-signalr/_static/image3.png)

Você deseja apenas uma instância das `StockTicker` classe a ser executada no servidor, portanto, você precisará configurar uma referência de cada `StockTickerHub` instância singleton `StockTicker` instância. O `StockTicker` classe precisa transmitir para clientes, pois ele contém os dados de estoque e dispara atualizações, mas `StockTicker` não é um `Hub` classe. O `StockTicker` classe precisar obter uma referência para o objeto de contexto de conexão do Hub do SignalR. Ele pode usar o objeto de contexto de conexão do SignalR para transmissão aos clientes.

#### <a name="create-stocktickerhubcs"></a>Criar StockTickerHub.cs

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.

1. Na **Adicionar Novo Item – SignalR.StockTicker**, selecione **instalado** > **Visual C#**   >  **Web**  >  **SignalR** e, em seguida, selecione **classe de Hub do SignalR (v2)**.

1. Nomeie a classe *StockTickerHub* e adicioná-lo ao projeto.

    Esta etapa cria o *StockTickerHub.cs* arquivo de classe. Ao mesmo tempo, ele adiciona um conjunto de arquivos de script e referências de assembly que dá suporte ao SignalR ao projeto.

1. Substitua o código na *StockTickerHub.cs* arquivo com este código:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. Salve o arquivo.

O aplicativo usa o [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) classe para definir métodos que os clientes podem chamar no servidor. Você está definindo um método: `GetAllStocks()`. Quando um cliente se conectará inicialmente ao servidor, ele chamará esse método para obter uma lista de todas as ações com seus preços atuais. O método pode ser executado de forma síncrona e retornar `IEnumerable<Stock>` porque ele está retornando os dados da memória.

Se o método tinha que obter os dados, fazendo algo que envolveria a espera, como uma pesquisa de banco de dados ou uma chamada de serviço web, você especificaria `Task<IEnumerable<Stock>>` como o valor de retorno para habilitar o processamento assíncrono. Para obter mais informações, consulte [o guia da API de Hubs de SignalR do ASP.NET - Server - quando executado de forma assíncrona](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).

O `HubName` atributo especifica como o aplicativo fará referência no Hub no código JavaScript no cliente. O nome padrão no cliente se você não usar esse atributo, é uma versão de camelCase do nome de classe, que nesse caso, seria `stockTickerHub`.

Como você verá mais tarde quando você cria o `StockTicker` classe, o aplicativo cria uma instância singleton da classe no seu estático `Instance` propriedade. Essa instância singleton do `StockTicker` está na memória, independentemente de quantos clientes conectar ou desconectar. Essa instância é o que o `GetAllStocks()` usa o método para retornar informações de estoque atuais.

#### <a name="create-stocktickercs"></a>Criar StockTicker.cs

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **classe**.

1. Nomeie a classe *StockTicker* e adicioná-lo ao projeto.

1. Substitua o código na *StockTicker.cs* arquivo com este código:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

Como todos os threads executará a mesma instância do código StockTicker, a classe StockTicker deve ser thread-safe.

### <a name="examine-the-server-code"></a>Examinar o código de servidor

Se você examinar o código do servidor, ele ajudará a entender como o aplicativo funciona.

#### <a name="storing-the-singleton-instance-in-a-static-field"></a>Armazenar a instância singleton em um campo estático

O código inicializa estático `_instance` campo que dá suporte a `Instance` propriedade com uma instância da classe. Como o construtor é particular, é a única instância da classe que o aplicativo pode criar. O aplicativo usa [inicialização lenta](/dotnet/framework/performance/lazy-initialization) para o `_instance` campo. Não é por motivos de desempenho. É garantir que a criação de instância é thread-safe.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

Sempre que um cliente se conecta ao servidor, uma nova instância da classe StockTickerHub em execução em um thread separado obtém a instância singleton StockTicker a `StockTicker.Instance` uma propriedade estática, como você viu anteriormente no `StockTickerHub` classe.

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a>Armazenar dados de estoque em um ConcurrentDictionary

O construtor inicializa o `_stocks` coleção com alguns dados de estoque de exemplo e `GetAllStocks` retorna as ações. Como você viu anteriormente, essa coleção de ações é retornada por `StockTickerHub.GetAllStocks`, que é um método de servidor no `Hub` classe que os clientes poderão chamar.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

A coleção de ações está definida como uma [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) tipo para acesso thread-safe. Como alternativa, você poderia usar um [dicionário](https://msdn.microsoft.com/library/xfhwa508.aspx) de objeto e bloquear explicitamente o dicionário quando você fizer alterações a ele.

Para este aplicativo de exemplo é Okey para armazenar dados de aplicativo na memória e a perda de dados quando o aplicativo descarta o `StockTicker` instância. Em um aplicativo real, você deve trabalhar com um repositório de dados back-end, como um banco de dados.

#### <a name="periodically-updating-stock-prices"></a>Atualizar periodicamente os preços de ações

O construtor é iniciado um `Timer` objeto que chama os métodos que atualizam os preços de ações de forma aleatória.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 `Timer` chamadas `UpdateStockPrices`, que passa nulo no parâmetro state. Antes de atualizar os preços, o aplicativo usa um bloqueio no `_updateStockPricesLock` objeto. O código verifica se outro thread já está atualizando os preços e, em seguida, ele chama `TryUpdateStockPrice` em cada ação na lista. O `TryUpdateStockPrice` método decide se deve alterar o preço da ação e quanto para alterá-lo. Se o preço da ação for alterada, o aplicativo chama `BroadcastStockPrice` difundir a alteração de preço de estoque para todos os clientes conectados.

O `_updatingStockPrices` sinalizador designado [volátil](https://msdn.microsoft.com/library/x13ttww7.aspx) para garantir que ele é thread-safe.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

Em um aplicativo real, o `TryUpdateStockPrice` um serviço web para pesquisar o preço seria chamada de método. Nesse código, o aplicativo usa um gerador de número aleatório para fazer alterações aleatoriamente.

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>Obtendo o contexto de SignalR, de modo que a classe StockTicker pode transmitir para clientes

Como as alterações de preço se originam aqui na `StockTicker` do objeto, ele é o objeto que precisa chamar uma `updateStockPrice` método em todos os clientes conectados. Em um `Hub` classe, que você tenha uma API para chamar métodos de cliente, mas `StockTicker` não deriva da `Hub` classe e não tem uma referência a qualquer `Hub` objeto. Difundir aos clientes conectados, o `StockTicker` classe precisar obter a instância de contexto do SignalR para o `StockTickerHub` de classe e usá-la para chamar métodos em clientes.

O código obtém uma referência ao contexto de SignalR quando ele cria a instância singleton da classe, passa que fazem referência ao construtor, e o construtor coloca-o no `Clients` propriedade.

Há duas razões por que você deseja obter o contexto de somente uma vez: obtendo o contexto é uma tarefa cara e colocá-los depois que faz com que o aplicativo preserva a ordem planejada de mensagens enviadas para os clientes.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

Introdução a `Clients` propriedade de contexto e colocá-lo na `StockTickerClient` propriedade permite que você escreva código para chamar métodos de cliente que se parece o mesmo como faria em um `Hub` classe. Por exemplo difundir a todos os clientes que você pode escrever `Clients.All.updateStockPrice(stock)`.

O `updateStockPrice` método que você está chamando em `BroadcastStockPrice` ainda não exista. Você vai adicioná-lo mais tarde quando você escreve o código que é executado no cliente. Você pode consultar `updateStockPrice` aqui porque `Clients.All` é dinâmico, o que significa que o aplicativo irá avaliar a expressão em tempo de execução. Quando esta chamada de método é executado, o SignalR enviará o nome do método e o valor do parâmetro para o cliente, e se o cliente tem um método chamado `updateStockPrice`, o aplicativo irá chamar esse método e passar o valor do parâmetro a ele.

`Clients.All` significa que envia a todos os clientes. O SignalR fornece outras opções para especificar quais clientes ou grupos de clientes para enviar para. Para obter mais informações, consulte [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).

### <a name="register-the-signalr-route"></a>Registre-se a rota de SignalR

O servidor precisa saber qual URL para interceptar e direcionar ao SignalR. Para fazer isso, adicione uma classe de inicialização OWIN:

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.

1. Na **Adicionar Novo Item – SignalR.StockTicker** selecionar **instalado** > **Visual C#**   >  **Web** e em seguida, selecione **classe de inicialização OWIN**.

1. Nomeie a classe *inicialização* e selecione **Okey**.

1. Substitua o código padrão a *Startup.cs* arquivo com este código:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

Você terminou de configurar o código do servidor. Na próxima seção, você irá configurar o cliente.

## <a name="set-up-the-client-code"></a>Configure o código de cliente

Nesta seção, você deve definir o código que é executado no cliente.

### <a name="create-the-html-page-and-javascript-file"></a>Criar a página HTML e um arquivo JavaScript

A página HTML exibirá os dados e o arquivo JavaScript organizará os dados.

#### <a name="create-stocktickerhtml"></a>Criar StockTicker.html

Primeiro, você adicionará o cliente HTML.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **página HTML**.

1. Nomeie o arquivo *StockTicker* e selecione **Okey**.

1. Substitua o código padrão a *StockTicker.html* arquivo com este código:

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    O HTML cria uma tabela com cinco colunas, uma linha de cabeçalho e uma linha de dados com uma única célula que abrange todas as cinco colunas. A linha de dados mostra "Carregando..." momentaneamente, quando o aplicativo é iniciado. O código JavaScript removerá essa linha e adicione em suas linhas de local com dados recuperados do servidor de ações.

    Especificam as marcas de script:

    * O arquivo de script do jQuery.

    * O arquivo de script do SignalR core.

    * O arquivo de script de proxies do SignalR.

    * Um arquivo de script StockTicker que você criará posteriormente.

    O aplicativo gera dinamicamente o arquivo de script de proxies do SignalR. Ele especifica a URL "hubs de signalr /" e define os métodos de proxy para os métodos na classe Hub, nesse caso, para `StockTickerHub.GetAllStocks`. Se você preferir, você pode gerar esse arquivo JavaScript manualmente usando [SignalR utilitários](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/). Não se esqueça de desativar a criação dinâmica de arquivos no `MapHubs` chamada de método.

1. Na **Gerenciador de soluções**, expanda **Scripts**.

    Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.

    > [!IMPORTANT]
    > O Gerenciador de pacotes instalará uma versão mais recente dos scripts do SignalR.

1. Atualize as referências de script no bloco de código para corresponder às versões dos arquivos de script no projeto.

1. Na **Gerenciador de soluções**, clique com botão direito *StockTicker.html*e, em seguida, selecione **Set as Start Page**.

#### <a name="create-stocktickerjs"></a>Criar StockTicker.js

Agora, crie o arquivo de JavaScript.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **arquivo JavaScript**.

1. Nomeie o arquivo *StockTicker* e selecione **Okey**.

1. Adicione este código para o *StockTicker.js* arquivo:

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a>Examinar o código do cliente

Se você examinar o código do cliente, ele ajudará a aprender como o código do cliente interage com o código do servidor para fazer com que o aplicativo funcione.

#### <a name="starting-the-connection"></a>Iniciando a conexão

`$.connection` refere-se para os proxies do SignalR. O código obtém uma referência para o proxy para o `StockTickerHub` de classe e o coloca no `ticker` variável. O nome do proxy é o nome que foi definido pelo `HubName` atributo:

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

Depois de definir todas as variáveis e funções, a última linha do código no arquivo inicializa a conexão do SignalR, chamando o SignalR `start` função. O `start` função executa de forma assíncrona e retorna um [jQuery adiado objeto](http://api.jquery.com/category/deferred-object/). Você pode chamar a função done para especificar a função ser chamada quando o aplicativo conclua a ação assíncrona.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a>Obter todas as ações

O `init` chamadas de função a `getAllStocks` função no servidor e usa as informações que o servidor retorna para atualizar a tabela de estoque. Observe que, por padrão, você precisa usar camelCasing no cliente, mesmo que o nome do método é o padrão Pascal-case no servidor. A regra camelCasing só se aplica aos métodos, não objetos. Por exemplo, você consultar `stock.Symbol` e `stock.Price`, e não `stock.symbol` ou `stock.price`.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

No `init` método, o aplicativo cria o HTML para uma linha da tabela para cada objeto de estoque recebido do servidor chamando `formatStock` às propriedades de formato da `stock` do objeto e, em seguida, chamando `supplant` substituir espaços reservados no `rowTemplate` variável com o `stock` valores de propriedade do objeto. O HTML resultante, em seguida, é acrescentado à tabela de estoque.

> [!NOTE]
> Você chama `init` passando a como uma `callback` função que executa após assíncrona `start` função é concluída. Se você chamasse `init` como uma instrução JavaScript separada depois de chamar `start`, a função falharia, pois ele seria executado imediatamente sem esperar que a função do início ao fim de estabelecer a conexão. Nesse caso, o `init` função tentar chamar o `getAllStocks` antes do aplicativo estabelece uma conexão de servidor de função.

#### <a name="getting-updated-stock-prices"></a>Obter cotações atualizadas

Quando o servidor alterará o preço de uma ação, ele chama o `updateStockPrice` em clientes conectados. O aplicativo adiciona a função à propriedade do cliente a `stockTicker` proxy para disponibilizá-lo para chamadas do servidor.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

O `updateStockPrice` formatos de função da mesma forma como em de linha de um objeto fixo recebido do servidor em uma tabela de `init` função. Em vez de acrescentar a linha na tabela, ele localiza a linha atual do estoque na tabela e substitui aquela linha por um novo.

## <a name="test-the-application"></a>Testar o aplicativo

Você pode testar o aplicativo para verificar se ele está funcionando. Você verá todas as janelas de navegador exibir a tabela de estoque em tempo real com cotações flutuando.

1. Na barra de ferramentas, ative **depuração de Script** e, em seguida, selecione o botão Reproduzir para executar o aplicativo no modo de depuração.

    ![Captura de tela do usuário ativar o modo de depuração e selecionando play.](tutorial-server-broadcast-with-signalr/_static/image4.png)

    Uma janela do navegador será aberta exibindo a **tabela de estoque ao vivo**. A tabela de estoque inicialmente mostra a linha "Carregando...", em seguida, após alguns instantes, o aplicativo mostra os dados iniciais de estoque e, em seguida, os preços das ações começam a mudar.

1. Copie a URL do navegador, abra dois outros navegadores e cole as URLs em barras de endereço.

    A exibição de estoque inicial é o mesmo que o navegador primeiro e as alterações ocorrem simultaneamente.

1. Feche todos os navegadores, abra um novo navegador e vá para a mesma URL.

    O objeto de singleton StockTicker continuou a ser executado no servidor. O **tabela de estoque ao vivo** mostra que as ações continuam a alterar. Você não vir a tabela inicial com zero alterar figuras.

1. Feche o navegador.

## <a name="enable-logging"></a>Habilite o registro em logs

O SignalR tem uma função de registro em log interno que pode ser habilitado no cliente para ajudar na solução de problemas. Nesta seção, você pode habilitar o log e ver exemplos que mostram como os logs de lhe dizer que um dos seguintes métodos de transporte está usando o SignalR:

* [WebSockets](http://en.wikipedia.org/wiki/WebSocket), com suporte pelo IIS 8 e navegadores atuais.

* [Eventos enviados pelo servidor](http://en.wikipedia.org/wiki/Server-sent_events), com suporte por navegadores diferentes do Internet Explorer.

* [Para sempre quadro](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), com suporte pelo Internet Explorer.

* [AJAX sondagem longa](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), com suporte por todos os navegadores.

Para qualquer determinada conexão, o SignalR escolhe o melhor método de transporte que dão suporte a servidor e o cliente.

1. Abra *StockTicker.js*.

1. Adicione esta linha de código para habilitar o log imediatamente antes do código que inicializa a conexão no final do arquivo realçada:

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. Pressione **F5** para executar o projeto.

1. Abra a janela de ferramentas de desenvolvedor do navegador e selecione o Console para ver os logs. Você talvez precise atualizar a página para ver os logs do SignalR negociar o método de transporte para uma nova conexão.

    * Se você estiver executando o Internet Explorer 10 no Windows 8 (IIS 8), o método de transporte está **WebSockets**.

    * Se você estiver executando o Internet Explorer 10 no Windows 7 (IIS 7.5), o método de transporte está **iframe**.

    * Se você estiver executando 19 Firefox no Windows 8 (IIS 8), o método de transporte é **WebSockets**.

        > [!TIP]
        > No Firefox, instale o suplemento Firebug para obter uma janela do Console.

    * Se você estiver executando 19 Firefox no Windows 7 (IIS 7.5), o método de transporte é **enviados ao servidor** eventos.

## <a name="install-the-stockticker-sample"></a>Instalar o exemplo StockTicker

O [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) instala o aplicativo StockTicker. O pacote NuGet inclui mais recursos que a versão simplificada que você criou do zero. Nesta seção do tutorial, instale o pacote NuGet e examine os novos recursos e o código que implementa-los.

> [!IMPORTANT]
> Se você instalar o pacote sem executar as etapas anteriores deste tutorial, você deve adicionar uma classe de inicialização do OWIN ao projeto. Este arquivo readme. txt para o pacote NuGet explica essa etapa.

### <a name="install-the-signalrsample-nuget-package"></a>Instale o pacote SignalR.Sample NuGet

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**.

1. No **Gerenciador de pacotes do NuGet: SignalR.StockTicker**, selecione **procurar**.

1. Partir **origem do pacote**, selecione **nuget.org**.

1. Insira *SignalR.Sample* na caixa de pesquisa e selecione **Microsoft.AspNet.SignalR.Sample** > **instalar**.

1. Na **Gerenciador de soluções**, expanda o *SignalR.Sample* pasta.

    Instalando o pacote SignalR.Sample criado na pasta e seu conteúdo.

1. No *SignalR.Sample* pasta, clique com botão direito *StockTicker.html*e, em seguida, selecione **definir como página inicial**.

    > [!NOTE]
    > Instalando o SignalR.Sample NuGet o pacote pode alterar a versão do jQuery que você tem em seu *Scripts* pasta. O novo *StockTicker.html* que instala o pacote no arquivo de *SignalR.Sample* pasta estará em sincronia com a versão do jQuery que instala o pacote, mas se você quiser executar original *StockTicker.html* arquivo novamente, talvez você precise atualizar a referência de jQuery na marca do script pela primeira vez.

### <a name="run-the-application"></a>Executar o aplicativo

 A tabela que você viu no primeiro aplicativo tinha recursos úteis. O aplicativo completo cotações da bolsa mostra os novos recursos: uma janela de rolagem horizontal que mostra os dados de estoque e estoque que alterar a cor como eles nascer e se encaixam.

1. Pressione **F5** para executar o aplicativo.

     Quando você executa o aplicativo pela primeira vez, o mercado de"" é "closed" e você verá uma tabela estática e uma janela de painel eletrônico que não é de rolagem.

1. Selecione **Open Market**.

    ![Captura de tela do ticker ao vivo.](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * O **Live Stock Ticker** caixa começa a rolar horizontalmente, e o servidor for iniciado difundir periodicamente as alterações de preço de estoque de forma aleatória.

    * Cada vez que um preço de ações é alterado, o aplicativo atualiza ambos o **tabela de estoque ao vivo** e o **Live Ticker de estoque**.

    * Quando a alteração de preço de uma ação for positiva, o aplicativo mostra a ação com um plano de fundo verde.

    * Quando a alteração for negativa, o aplicativo mostra o estoque com fundo vermelho.

1. Selecione **fechar mercado**.

    * A tabela de atualizações de parada.

    * O painel eletrônico para rolagem.

1. Selecione **redefinir**.

    * Todos os dados de estoque é redefinido.

    * O aplicativo restaura o estado inicial antes das alterações de preço iniciado.

1. Copie a URL do navegador, abra dois outros navegadores e cole as URLs em barras de endereço.

1. Você ver os mesmos dados são atualizados dinamicamente ao mesmo tempo em cada navegador.

1. Quando você seleciona qualquer um dos controles, todos os navegadores respondem da mesma forma ao mesmo tempo.

### <a name="live-stock-ticker-display"></a>Exibição de painel eletrônico de estoque ao vivo

O **Live Stock Ticker** exibição é uma lista não ordenada em um `<div>` elemento formatado em uma única linha pelos estilos CSS. O aplicativo inicializa e atualiza o painel eletrônico da mesma forma que a tabela:, substituindo espaços reservados em uma `<li>` cadeia de caracteres de modelo e adicionar dinamicamente a `<li>` elementos a serem o `<ul>` elemento. O aplicativo inclui a rolagem usando o jQuery `animate` função para variar a margem esquerda da lista não ordenada de dentro do `<div>`.

#### <a name="signalrsample-stocktickerhtml"></a>SignalR.Sample StockTicker.html

A cotação da bolsa código HTML:

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a>SignalR.Sample StockTicker.css

A cotação da bolsa código CSS:

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a>SignalR.Sample SignalR.StockTicker.js

O código jQuery que torna role:

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>Métodos adicionais no servidor que o cliente pode chamar

Para adicionar flexibilidade ao aplicativo, há métodos adicionais que o aplicativo pode chamar.

#### <a name="signalrsample-stocktickerhubcs"></a>SignalR.Sample StockTickerHub.cs

O `StockTickerHub` classe define quatro métodos adicionais que o cliente pode chamar:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

O aplicativo chama `OpenMarket`, `CloseMarket`, e `Reset` em resposta aos botões na parte superior da página. Eles demonstram o padrão de um cliente disparar uma alteração no estado propagada imediatamente para todos os clientes. Cada um desses métodos chama um método no `StockTicker` classe que faz com que a alteração de estado de colocação no mercado e, em seguida, transmite o novo estado.

#### <a name="signalrsample-stocktickercs"></a>SignalR.Sample StockTicker.cs

No `StockTicker` classe, o aplicativo mantém o estado do mercado com um `MarketState` propriedade que retorna um `MarketState` valor de enumeração:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

Cada um dos métodos que alteram o estado de mercado fazê-lo dentro de um bloco de bloqueio porque o `StockTicker` classe deve ser thread-safe:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

Para garantir que esse código é thread-safe, o `_marketState` campo que dá suporte a `MarketState` propriedade designada `volatile`:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

O `BroadcastMarketStateChange` e `BroadcastMarketReset` métodos são semelhantes ao método BroadcastStockPrice que você já viu, exceto que eles chamam métodos diferentes definidos no cliente:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>Funções adicionais no cliente que o servidor pode chamar

O `updateStockPrice` função agora manipula a tabela e a exibição de painel eletrônico e usa `jQuery.Color` Flash cores vermelhas e verdes.

Novas funções no *SignalR.StockTicker.js* habilitar e desabilitar os botões com base no estado de colocação no mercado. Eles também interromper ou iniciar o **Live Stock Ticker** rolagem horizontal. Uma vez que muitas funções estão sendo adicionadas ao `ticker.client`, o aplicativo usa o [jQuery estender a função](http://api.jquery.com/jQuery.extend/) para adicioná-los.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>Instalação de cliente adicionais depois de estabelecer a conexão

Depois que o cliente estabelece a conexão, ele tem algum trabalho adicional para fazer:

* Descubra se o mercado está aberto ou fechado para chamar o `marketOpened` ou `marketClosed` função.

* Anexe as chamadas de método do servidor para os botões.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

Os métodos de servidor não são vinculei os botões até depois que o aplicativo estabelece a conexão. É assim que o código não é possível chamar os métodos de servidor antes de estarem disponíveis.

## <a name="additional-resources"></a>Recursos adicionais

Neste tutorial, você aprendeu como programar um aplicativo de SignalR que transmite mensagens do servidor para todos os clientes conectados. Agora você pode transmitir mensagens em intervalos periódicos e em resposta a notificações de qualquer cliente. Você pode usar o conceito de instância singleton multi-threaded para manter o estado do servidor em cenários de jogos online de vários jogadores. Por exemplo, consulte [ShootR jogo com base no SignalR](https://github.com/NTaylorMullen/ShootR).

Para obter tutoriais que mostram os cenários de comunicação peer-to-peer, consulte [Introdução ao SignalR](introduction-to-signalr.md) e [a atualização em tempo real com SignalR](tutorial-high-frequency-realtime-with-signalr.md).

Para obter mais informações sobre o SignalR, consulte os seguintes recursos:

* [SignalR do ASP.NET](../../index.md)
* [Projeto de SignalR](http://signalr.net/)
* [GitHub do SignalR e exemplos](https://github.com/SignalR/SignalR)
* [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Criar o projeto
> * Configure o código de servidor
> * Examinado o código do servidor
> * Configure o código de cliente
> * Examinado o código do cliente
> * Testando o aplicativo
> * Registro em log habilitado

Avance para o próximo artigo para saber como criar um aplicativo web em tempo real que usa o ASP.NET SignalR 2.
> [!div class="nextstepaction"]
> [Criar aplicativo web em tempo real com SignalR](real-time-web-applications-with-signalr.md)