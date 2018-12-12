---
uid: signalr/overview/older-versions/tutorial-server-broadcast-with-aspnet-signalr
title: 'Tutorial: Transmissão de servidor com SignalR do ASP.NET 1.x | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como criar um aplicativo web que usa o SignalR do ASP.NET para fornecer funcionalidade de difusão de servidor. Significa que communic de transmissão de servidor...
ms.author: riande
ms.date: 04/10/2013
ms.assetid: ab7b2554-956a-4f6d-b2a0-4ae0c62e8580
msc.legacyurl: /signalr/overview/older-versions/tutorial-server-broadcast-with-aspnet-signalr
msc.type: authoredcontent
ms.openlocfilehash: d5c09f6493c4dedb4fc2ecbf9055dd014ede71f2
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53288040"
---
<a name="tutorial-server-broadcast-with-aspnet-signalr-1x"></a>Tutorial: Transmissão de servidor com SignalR do ASP.NET 1.x
====================
por [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tutorial mostra como criar um aplicativo web que usa o SignalR do ASP.NET para fornecer funcionalidade de difusão de servidor. Transmissão de servidor significa que as comunicações enviadas para os clientes são iniciadas pelo servidor. Esse cenário requer uma abordagem de programação diferente que peer-to-peer cenários como aplicativos de bate-papo, em que as comunicações enviadas para os clientes são iniciadas por um ou mais dos clientes.
> 
> O aplicativo que você criará neste tutorial simula uma bolsa, um cenário típico para a funcionalidade de difusão de servidor.
> 
> O tutorial, os comentários são boas-vindos. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com).


## <a name="overview"></a>Visão geral

O [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) pacote NuGet instala um aplicativo de cotação da bolsa de exemplo simulado em um projeto do Visual Studio. A primeira parte deste tutorial, você criará uma versão simplificada do aplicativo do zero. O restante do tutorial, você instalar o pacote do NuGet e examine os recursos adicionais e o código que ele cria.

O aplicativo de cotação da bolsa é um representante de um tipo de aplicativo em tempo real no qual você deseja periodicamente as notificações "push" ou difusão, do servidor para todos os clientes conectados.

O aplicativo que você criará na primeira parte deste tutorial exibe uma grade com dados de estoque.

![Versão inicial do StockTicker](tutorial-server-broadcast-with-aspnet-signalr/_static/image1.png)

Periodicamente, o servidor de atualizações de preços de ações aleatoriamente e envia as atualizações para todos os clientes conectados. No navegador, os números e símbolos na **alterar** e **%** colunas alteram dinamicamente em resposta a notificações do servidor. Se você abrir navegadores adicionais para a mesma URL, todos eles mostram os mesmos dados e as mesmas alterações aos dados simultaneamente.

Este tutorial contém as seções a seguir:

- [Pré-requisitos](#prerequisites)
- [Criar o projeto](#createproject)
- [Adicione os pacotes NuGet do SignalR](#nugetpackages)
- [Configure o código de servidor](#server)
- [Configure o código de cliente](#client)
- [Testar o aplicativo](#test)
- [Habilitar registro em log](#enablelogging)
- [Instalar e examine o exemplo completo de StockTicker](#fullsample)
- [Próximas etapas](#nextsteps)

> [!NOTE]
> Se você não quiser trabalhar com as etapas de criação do aplicativo, você pode instalar o pacote de SignalR.Sample em uma nova **aplicativo Web ASP.NET vazio** de projeto e ler essas etapas para obter explicações do código. A primeira parte do tutorial abrange um subconjunto do código SignalR.Sample e a segunda parte explica os principais recursos da funcionalidade adicional no pacote SignalR.Sample.


<a id="prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que você tenha o Visual Studio 2012 ou 2010 SP1 instalado no seu computador. Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2012 Express gratuito da Web.

Se você tiver o Visual Studio 2010, verifique se [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) está instalado.

<a id="createproject"></a>

## <a name="create-the-project"></a>Criar o projeto

1. Dos **arquivo** menu, clique em **novo projeto**.
2. No **novo projeto** diálogo caixa, expanda **c#** sob **modelos** e selecione **Web**.
3. Selecione o **aplicativo Web vazio ASP.NET** modelo, o nome do projeto *SignalR.StockTicker*e clique em **Okey**.

    ![Caixa de diálogo Novo Projeto](tutorial-server-broadcast-with-aspnet-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-nuget-packages"></a>Adicione os pacotes NuGet do SignalR

### <a name="add-the-signalr-and-jquery-nuget-packages"></a>Adicionar o SignalR e pacotes do NuGet do JQuery

Você pode adicionar funcionalidade de SignalR para um projeto por meio da instalação de um pacote do NuGet.

1. Clique em **ferramentas | Gerenciador de pacotes NuGet | Package Manager Console**.
2. Digite o seguinte comando no Gerenciador de pacotes.

    [!code-powershell[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample1.ps1)]

    O pacote do SignalR instala um número de outros pacotes do NuGet como dependências. Quando a instalação for concluída você tem todos os componentes de servidor e cliente necessários para usar o SignalR em um aplicativo ASP.NET.

<a id="server"></a>

## <a name="set-up-the-server-code"></a>Configure o código de servidor

Nesta seção você configurar o código que é executado no servidor.

### <a name="create-the-stock-class"></a>Criar a classe de estoque

Você começa criando a classe de modelo de estoque que você usará para armazenar e transmitir informações sobre uma ação.

1. Crie um novo arquivo de classe na pasta do projeto, nomeie- *Stock.cs*e, em seguida, substitua o código de modelo pelo código a seguir:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample2.cs)]

    As duas propriedades que você definirá quando você cria as ações são o símbolo (por exemplo, MSFT da Microsoft) e o preço. As outras propriedades dependem de como e quando você definir o preço. Na primeira vez em que você definir o preço, o valor é propagado para DayOpen. Próximas vezes quando você define o preço, a alteração e os valores de propriedade PercentChange são calculados com base na diferença entre preço e DayOpen.

### <a name="create-the-stockticker-and-stocktickerhub-classes"></a>Criar as classes StockTicker e StockTickerHub

Você usará a API de Hub do SignalR para manipular a interação do servidor para cliente. Uma classe StockTickerHub que deriva da classe SignalR Hub manipulará receber chamadas de método e de conexões de clientes. Você também precisa manter dados de estoque e executar um objeto de Timer para disparar periodicamente atualizações de preço, independentemente das conexões de cliente. Você não pode colocar essas funções em uma classe Hub, como instâncias de Hub são transitórias. Uma instância da classe Hub é criada para cada operação no hub, como conexões e chamadas do cliente para o servidor. Portanto, o mecanismo que mantém os dados de estoque, atualiza os preços e transmite as atualizações de preço deve ser executado em uma classe separada, que você nomeará StockTicker.

![Transmitindo de StockTicker](tutorial-server-broadcast-with-aspnet-signalr/_static/image4.png)

Você precisará apenas uma instância da classe StockTicker para ser executado no servidor, portanto, você precisará configurar uma referência de cada instância de StockTickerHub para a instância do singleton StockTicker. A classe StockTicker deve ser capaz de fazer uma transmissão para os clientes porque ele tem os dados de estoque e dispara atualizações, mas StockTicker não é uma classe de Hub. Portanto, a classe StockTicker tem que obter uma referência ao objeto de contexto de conexão de Hub do SignalR. Ele pode usar o objeto de contexto de conexão do SignalR para transmissão aos clientes.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **Adicionar Novo Item**.
2. Se você tiver o Visual Studio 2012 com o [ASP.NET e Web Tools 2012.2 atualização](https://go.microsoft.com/fwlink/?LinkId=279941), clique em **Web** sob **Visual c#** e selecione o **SignalR Hub classe** modelo de item. Caso contrário, selecione a **classe** modelo.
3. Nomeie a nova classe *StockTickerHub.cs*e, em seguida, clique em **Add**.

    ![Adicionar StockTickerHub.cs](tutorial-server-broadcast-with-aspnet-signalr/_static/image5.png)
4. Substitua o código de modelo pelo código a seguir:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample3.cs)]

    O [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) classe é usada para definir métodos que os clientes podem chamar no servidor. Você está definindo um método: `GetAllStocks()`. Quando um cliente se conectará inicialmente ao servidor, ele chamará esse método para obter uma lista de todas as ações com seus preços atuais. O método pode executar de forma síncrona e retornar `IEnumerable<Stock>` porque ele está retornando os dados da memória. Se o método tinha que obter os dados, fazendo algo que envolveria a espera, como uma pesquisa de banco de dados ou uma chamada de serviço web, você especificaria `Task<IEnumerable<Stock>>` como o valor de retorno para habilitar o processamento assíncrono. Para obter mais informações, consulte [o guia da API de Hubs de SignalR do ASP.NET - Server - quando executado de forma assíncrona](index.md).

    O atributo HubName Especifica como o Hub será referenciado no código JavaScript no cliente. O nome padrão no cliente se você não usar esse atributo é uma versão em camel case do nome da classe, que nesse caso, seria stockTickerHub.

    Como você verá mais tarde quando você cria a classe StockTicker, uma instância singleton da classe é criada em sua propriedade de instância estática. Que instância singleton da StockTicker permanece na memória, independentemente de quantos clientes conectar ou desconectar, e essa instância é o que o método GetAllStocks usa para retornar informações de estoque atuais.
5. Crie um novo arquivo de classe na pasta do projeto, nomeie- *StockTicker.cs*e, em seguida, substitua o código de modelo pelo código a seguir:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample4.cs)]

    Uma vez que vários threads executará a mesma instância do código StockTicker, a classe StockTicker deve ser threadsafe.

    ### <a name="storing-the-singleton-instance-in-a-static-field"></a>Armazenar a instância singleton em um campo estático

    O código inicializa estático \_campo de instância que sustenta a propriedade de instância com uma instância da classe e isso é a única instância da classe que pode ser criada, porque o construtor está marcado como privado. [Inicialização lenta](https://msdn.microsoft.com/library/dd997286.aspx) é usado para o \_campo de instância, não por motivos de desempenho, mas para assegurar que a criação de instância está threadsafe.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample5.cs)]

    Sempre que um cliente se conecta ao servidor, uma nova instância da classe StockTickerHub em execução em um thread separado obtém a instância singleton de StockTicker da propriedade StockTicker.Instance estática, como você viu anteriormente na classe StockTickerHub.

    ### <a name="storing-stock-data-in-a-concurrentdictionary"></a>Armazenar dados de estoque em um ConcurrentDictionary

    O construtor inicializa o \_coleção de ações com alguns dados de estoque de exemplo e GetAllStocks retorna as ações. Como você viu anteriormente, essa coleção de ações por sua vez é retornada pelo StockTickerHub.GetAllStocks que é um método de servidor na classe Hub que os clientes poderão chamar.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample6.cs)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample7.cs)]

    A coleção de ações está definida como uma [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) tipo para acesso thread-safe. Como alternativa, você poderia usar um [dicionário](https://msdn.microsoft.com/library/xfhwa508.aspx) de objeto e bloquear explicitamente o dicionário quando você fizer alterações a ele.

    Para este aplicativo de exemplo, Okey é para armazenar dados de aplicativo na memória e os dados perdidos quando a instância de StockTicker é descartada. Em um aplicativo real, você trabalhará com um armazenamento de dados back-end como um banco de dados.

    ### <a name="periodically-updating-stock-prices"></a>Atualizar periodicamente os preços de ações

    O construtor inicia um objeto de Timer que chama os métodos que atualizam os preços de ações de forma aleatória.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample8.cs)]

    UpdateStockPrices é chamado pelo Timer, que transmite null no parâmetro state. Antes de atualizar os preços, um bloqueio é usado no \_updateStockPricesLock objeto. O código verifica se outro thread já está atualizando os preços e, em seguida, ele chama TryUpdateStockPrice em cada ação na lista. O método TryUpdateStockPrice decide se deve alterar o preço da ação e quanto para alterá-lo. Se o preço da ação for alterado, BroadcastStockPrice é chamado para a alteração de preço de estoque de difusão para todos os clientes conectados.

    O \_updatingStockPrices sinalizador está marcado como [volátil](https://msdn.microsoft.com/library/x13ttww7.aspx) para garantir que o acesso a ele seja threadsafe.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample9.cs)]

    Em um aplicativo real, o método TryUpdateStockPrice poderia chamar um serviço web para pesquisar o preço; Nesse código, ele usa um gerador de número aleatório para fazer alterações aleatoriamente.

    ### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>Obtendo o contexto de SignalR, de modo que a classe StockTicker pode transmitir para clientes

    Como as alterações de preço se originam aqui no objeto StockTicker, esse é o objeto que precisa chamar um método updateStockPrice em todos os clientes conectados. Em uma classe Hub, você tem uma API para chamar métodos de cliente, mas StockTicker não deriva da classe Hub e não tem uma referência a qualquer objeto de Hub. Portanto, para transmitir para clientes conectados, a classe StockTicker tem que obter a instância de contexto do SignalR para a classe StockTickerHub e usá-la para chamar métodos em clientes.

    O código obtém uma referência ao contexto de SignalR ao criar a instância singleton da classe, passa que fazem referência ao construtor, e o construtor coloca na propriedade de clientes.

    Há duas razões por que você deseja obter o contexto de apenas uma vez: obtendo o contexto é uma operação dispendiosa e fazê-lo uma vez assegura que a ordem planejada de mensagens enviadas para clientes seja preservada.

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample10.cs)]

    Obtendo a propriedade de clientes do contexto e colocá-lo na propriedade StockTickerClient permitem escrever código para chamar métodos de cliente que tem a mesma aparência como faria em uma classe de Hub. Por exemplo, para difundir a todos os clientes, você pode escrever Clients.All.updateStockPrice(stock).

    O método updateStockPrice que você está chamando em BroadcastStockPrice ainda; não existe Você vai adicioná-lo mais tarde quando você escreve o código que é executado no cliente. Você pode consultar updateStockPrice aqui porque Clients.All é dinâmica, o que significa que a expressão será avaliada em tempo de execução. Quando esta chamada de método é executado, o SignalR enviará o nome do método e o valor do parâmetro para o cliente e se o cliente tem um método chamado updateStockPrice, esse método será chamado e o valor do parâmetro será passado para ele.

    Clients.All significa enviar para todos os clientes. O SignalR fornece outras opções para especificar quais clientes ou grupos de clientes para enviar para. Para obter mais informações, consulte [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).

### <a name="register-the-signalr-route"></a>Registre-se a rota de SignalR

O servidor precisa saber qual URL para interceptar e direcionar ao SignalR. Para fazer o que você adicionará um código para o *global. asax* arquivo.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e, em seguida, clique em **Adicionar Novo Item**.
2. Selecione o **classe de aplicativo Global** modelo de item e, em seguida, clique em **Add**.

    ![Adicionar global. asax](tutorial-server-broadcast-with-aspnet-signalr/_static/image6.png)
3. Adicione o código de registro de rota de SignalR ao aplicativo\_método Start:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample11.cs)]

    Por padrão, a URL base para todo o tráfego de SignalR é "/ signalr", e "hubs de signalr /" são usados para recuperar um arquivo JavaScript gerado dinamicamente que define os proxies para todos os Hubs, você tem em seu aplicativo. O método MapHubs inclui sobrecargas que permitem que você especifique uma URL de base diferente e determinadas opções de SignalR em uma instância das [HubConfiguration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubconfiguration(v=vs.111).aspx) classe.
4. Adicionar uma instrução na parte superior do arquivo:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample12.cs)]
5. Salve e feche o *global. asax* de arquivo e compile o projeto.

Agora você concluiu a configuração de código do servidor. Na próxima seção, você irá configurar o cliente.

<a id="client"></a>

## <a name="set-up-the-client-code"></a>Configure o código de cliente

1. Criar um novo arquivo HTML na pasta do projeto e denomine *StockTicker.html*.
2. Substitua o código de modelo pelo código a seguir:

    [!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample13.html)]

    O HTML cria uma tabela com 5 colunas, uma linha de cabeçalho e uma linha de dados com uma única célula que abrange todas as colunas de 5. A linha de dados exibe "Carregando..." e só será exibida momentaneamente, quando o aplicativo é iniciado. O código JavaScript removerá essa linha e adicione em suas linhas de local com dados recuperados do servidor de ações.

    As marcas de script especificam o arquivo de script do jQuery, o arquivo de script do SignalR core, o arquivo de script do SignalR proxies e um arquivo de script StockTicker que você criará posteriormente. O arquivo de script de proxies do SignalR, que especifica a URL "hubs de signalr /", é gerado dinamicamente e define os métodos de proxy para os métodos na classe Hub, nesse caso para StockTickerHub.GetAllStocks. Se você preferir, você pode gerar esse arquivo JavaScript manualmente usando [SignalR utilitários](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) e desativar a criação de arquivo dinâmico na chamada do método MapHubs.
3. > [!IMPORTANT]
   > Certifique-se de que o arquivo JavaScript faz referência na *StockTicker.html* estão corretos. Ou seja, certifique-se de que a versão do jQuery em sua marca de script (1.8.2 no exemplo) é a mesma versão do jQuery em seu projeto *Scripts* pasta e certifique-se de que a versão do SignalR em sua marca de script é o mesmo que o SignalR versão em seu projeto *Scripts* pasta. Altere os nomes de arquivo nas marcas de script, se necessário.
4. Na **Gerenciador de soluções**, clique com botão direito *StockTicker.html*e, em seguida, clique em **Set as Start Page**.
5. Crie um novo arquivo JavaScript na pasta do projeto e denomine *StockTicker.js*...
6. Substitua o código de modelo pelo código a seguir:

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample14.js)]

    $.connection refere-se para os proxies do SignalR. O código obtém uma referência ao proxy para a classe StockTickerHub e coloca-o na variável da bolsa. O nome do proxy é o nome que foi definido pelo atributo [HubName]:

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample15.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample16.cs)]

    Depois que todas as variáveis e funções são definidas, a última linha do código no arquivo inicializa a conexão do SignalR, chamando a função de início do SignalR. Função inicial executa de forma assíncrona e retorna um [jQuery adiado objeto](http://api.jquery.com/category/deferred-object/), que significa que você pode chamar a função done para especificar a função ser chamada quando a operação assíncrona é concluída...

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample17.js)]

    Função init chama a função getAllStocks no servidor e usa as informações que o servidor retorna para atualizar a tabela de estoque. Observe que, por padrão, você precisa usar a concatenação com maiusculas e minúsculas no cliente Embora o nome do método é o padrão Pascal-case no servidor. A regra de Camel Case só se aplica aos métodos, não objetos. Por exemplo, você se referir ao estoque. Símbolo e estoque. Preço, não stock.symbol ou stock.price.

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample18.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample19.cs)]

    Se você quisesse usar maiusculas e minúsculas de pascal no cliente, ou se você quisesse usar um nome de método completamente diferente, você pode decorar o método de Hub com o atributo HubMethodName da mesma forma você decorada a própria classe de Hub com o atributo HubName.

    O método init, HTML para uma linha da tabela é criada para cada objeto de estoque recebido do servidor por chamada formatStock às propriedades de formato do objeto de estoque e, em seguida, chamando suplantar (que é definido na parte superior da *StockTicker.js*) para substituir os espaços reservados na variável rowTemplate com os valores de propriedade do objeto de estoque. O HTML resultante, em seguida, é acrescentado à tabela de estoque.

    Para chamar init, passá-la como uma função de retorno de chamada que é executado depois que a função de início assíncrono é concluída. Se você tiver chamado init como uma instrução JavaScript separada depois de chamar o início, a função falharia, pois ele seria executado imediatamente sem esperar que a função do início ao fim de estabelecer a conexão. Nesse caso, a função init tentaria chamar a função getAllStocks antes que a conexão de servidor é estabelecida.

    Quando o servidor alterará o preço de uma ação, ela chama o updateStockPrice clientes conectados. A função é adicionada para a propriedade do cliente do proxy stockTicker para disponibilizá-lo para chamadas do servidor.

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample20.js)]

    A função updateStockPrice formata um objeto fixo recebido do servidor em uma linha da tabela da mesma forma como na função init. No entanto, em vez de acrescentar a linha na tabela, ele localiza a linha atual do estoque na tabela e substitui aquela linha por um novo.

<a id="test"></a>

## <a name="test-the-application"></a>Testar o aplicativo

1. Pressione F5 para executar o aplicativo no modo de depuração.

    A tabela de estoque exibe inicialmente a "Carregando..." linha, em seguida, após um pequeno atraso, que os dados de estoque iniciais são exibidos e, em seguida, os preços das ações começam a mudar.

    ![Carregando](tutorial-server-broadcast-with-aspnet-signalr/_static/image7.png)

    ![Tabela de estoque inicial](tutorial-server-broadcast-with-aspnet-signalr/_static/image8.png)

    ![Tabela de estoque receber alterações do servidor](tutorial-server-broadcast-with-aspnet-signalr/_static/image9.png)
2. Copie a URL da barra de endereços do navegador e cole-o em um ou mais novas janelas de navegador.

    A exibição de estoque inicial é o mesmo que o navegador primeiro e as alterações ocorrem simultaneamente.
3. Feche todos os navegadores e abra um novo navegador e vá para a mesma URL.

    O objeto de singleton StockTicker continua a executar no servidor, portanto a exibição de tabela de estoque mostra que as ações continuam a alterar. (Você não ver a tabela inicial com zero alterar figuras.)
4. Feche o navegador.

<a id="enablelogging"></a>

## <a name="enable-logging"></a>Habilite o registro em logs

O SignalR tem uma função de registro em log interno que pode ser habilitado no cliente para ajudar na solução de problemas. Nesta seção, você habilita o log e ver exemplos que mostram como os logs de lhe dizer que um dos seguintes métodos de transporte está usando o SignalR:

- [WebSockets](http://en.wikipedia.org/wiki/WebSocket), com suporte pelo IIS 8 e navegadores atuais.
- [Eventos enviados pelo servidor](http://en.wikipedia.org/wiki/Server-sent_events), com suporte por navegadores diferentes do Internet Explorer.
- [Para sempre quadro](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), com suporte pelo Internet Explorer.
- [AJAX sondagem longa](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), com suporte por todos os navegadores.

Para qualquer determinada conexão, o SignalR escolhe o melhor método de transporte que dão suporte a servidor e o cliente.

1. Abra *StockTicker.js* e adicione uma linha de código para habilitar o log imediatamente antes do código que inicializa a conexão no final do arquivo:

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample21.js)]
2. Pressione F5 para executar o projeto.
3. Abra a janela de ferramentas de desenvolvedor do navegador e selecione o Console para ver os logs. Você talvez precise atualizar a página para ver os logs do Signalr negociar o método de transporte para uma nova conexão.

    Se você estiver executando o Internet Explorer 10 no Windows 8 (IIS 8), o método de transporte é WebSockets.

    ![Console do 10 IIS IE 8](tutorial-server-broadcast-with-aspnet-signalr/_static/image10.png)

    Se você estiver executando o Internet Explorer 10 no Windows 7 (IIS 7.5), o método de transporte é iframe.

    ![IE 10 Console, IIS 7.5](tutorial-server-broadcast-with-aspnet-signalr/_static/image11.png)

    No Firefox, instale o suplemento Firebug para obter uma janela do Console. Se você estiver executando 19 Firefox no Windows 8 (IIS 8), o método de transporte é WebSockets.

    ![Firefox 19 IIS 8 Websockets](tutorial-server-broadcast-with-aspnet-signalr/_static/image12.png)

    Se você estiver executando 19 Firefox no Windows 7 (IIS 7.5), o método de transporte é eventos enviados pelo servidor.

    ![Console do Firefox 19 IIS 7.5](tutorial-server-broadcast-with-aspnet-signalr/_static/image13.png)

<a id="fullsample"></a>

## <a name="install-and-review-the-full-stockticker-sample"></a>Instalar e examine o exemplo completo de StockTicker

O aplicativo StockTicker instalado pelo [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) pacote NuGet inclui mais recursos que a versão simplificada que você acabou de criar do zero. Nesta seção do tutorial, instale o pacote NuGet e examine os novos recursos e o código que implementa-los.

### <a name="install-the-signalrsample-nuget-package"></a>Instale o pacote SignalR.Sample NuGet

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **Manage NuGet Packages**.
2. No **gerenciar pacotes NuGet** caixa de diálogo, clique em **Online**, insira *SignalR.Sample* no **Pesquisar Online** caixa e, em seguida, clique em  **Instale** no **SignalR.Sample** pacote.

    ![Instalar pacote SignalR.Sample](tutorial-server-broadcast-with-aspnet-signalr/_static/image14.png)
3. No *global. asax* do arquivo, comente o RouteTable.Routes.MapHubs(); que você adicionou anteriormente no aplicativo de linha\_método Start.

    O código na *global. asax* não for mais necessário porque o pacote SignalR.Sample registra o roteiro de SignalR na *App\_Start/RegisterHubs.cs* arquivo:

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample22.cs)]

    A classe WebActivator que é referenciada pelo atributo de assembly está incluída no pacote WebActivatorEx NuGet, que é instalado como uma dependência do pacote SignalR.Sample.
4. Na **Gerenciador de soluções**, expanda o *SignalR.Sample* pasta que foi criada pela instalação do pacote SignalR.Sample.
5. No *SignalR.Sample* pasta, clique com botão direito *StockTicker.html*e, em seguida, clique em **definir como página inicial**.

    > [!NOTE]
    > Instalando o SignalR.Sample NuGet o pacote pode alterar a versão do jQuery que você tem em seu *Scripts* pasta. O novo *StockTicker.html* que instala o pacote no arquivo de *SignalR.Sample* pasta estará em sincronia com a versão do jQuery que instala o pacote, mas se você quiser executar original *StockTicker.html* arquivo novamente, talvez você precise atualizar a referência de jQuery na marca do script pela primeira vez.

### <a name="run-the-application"></a>Executar o aplicativo

1. Pressione F5 para executar o aplicativo.

    Além de grade que você viu anteriormente, o aplicativo completo cotações da bolsa mostra uma janela de rolagem horizontal que exibe os mesmos dados de estoque. Quando você executa o aplicativo pela primeira vez, o mercado de"" é "closed" e você verá uma grade estática e uma janela de painel eletrônico que não é de rolagem.

    ![Início da tela de StockTicker](tutorial-server-broadcast-with-aspnet-signalr/_static/image15.png)

    Quando você clica em **Open Market**, o **Live Stock Ticker** caixa começa a rolar horizontalmente, e o servidor for iniciado difundir periodicamente as alterações de preço de estoque de forma aleatória. Cada vez que um preço de ação for alterado, ambos o **tabela de estoque em tempo real** grade e o **Live Ticker de estoque** caixa são atualizados. Quando a alteração de preço de uma ação for positiva, o estoque é mostrado com um plano de fundo verde, e quando a alteração for negativa, o estoque é mostrado com um plano de fundo vermelho.

    ![Abra o aplicativo StockTicker, mercado](tutorial-server-broadcast-with-aspnet-signalr/_static/image16.png)

    O **fechar mercado** botão interrompe as alterações e interrompe a rolagem da bolsa e o **redefinir** botão redefine todos os dados de estoque para o estado inicial antes de iniciar alterações de preços. Se você abrir mais janelas de navegador e vá para a mesma URL, você verá os mesmos dados são atualizados dinamicamente ao mesmo tempo em cada navegador. Quando você clica em um dos botões, todos os navegadores respondem da mesma forma, ao mesmo tempo.

### <a name="live-stock-ticker-display"></a>Exibição de painel eletrônico de estoque ao vivo

O **Live Stock Ticker** exibição é uma lista não ordenada em um elemento div que é formatado em uma única linha por estilos CSS. O ticker será inicializada e atualizada da mesma forma que a tabela:, substituindo espaços reservados em uma &lt;li&gt; cadeia de caracteres de modelo e adicionar dinamicamente a &lt;li&gt; elementos a serem o &lt;ul&gt; elemento. A rolagem é realizada por meio da função de animação do jQuery para variar a margem esquerda da lista não ordenada de dentro do div.

A cotação da bolsa HTML:

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample23.html)]

A cotação da bolsa CSS:

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample24.html)]

O código jQuery que torna role:

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample25.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>Métodos adicionais no servidor que o cliente pode chamar

A classe StockTickerHub define quatro métodos adicionais que o cliente pode chamar:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample26.cs)]

OpenMarket, CloseMarket e redefinição são chamados em resposta aos botões na parte superior da página. Eles demonstram o padrão de um cliente disparar uma alteração no estado imediatamente é propagado para todos os clientes. Cada um desses métodos chama um método na classe StockTicker que afetam o estado do mercado, alterar e, em seguida, transmite o novo estado.

Na classe StockTicker, o estado do mercado é mantido por uma propriedade MarketState que retorna um valor de enumeração MarketState:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample27.cs)]

Cada um dos métodos que alteram o estado de mercado fazer isso dentro de um bloco de bloqueio porque a classe StockTicker deve ser threadsafe:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample28.cs)]

Para garantir que esse código é threadsafe, o \_marketState campo que sustenta a propriedade MarketState é marcado como volátil,

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample29.cs)]

Os métodos BroadcastMarketStateChange e BroadcastMarketReset são semelhantes ao método BroadcastStockPrice que você já viu, exceto que eles chamam métodos diferentes definidos no cliente:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample30.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>Funções adicionais no cliente que o servidor pode chamar

A função updateStockPrice agora manipula a grade e a exibição de painel eletrônico e usa jQuery.Color Flash cores vermelhas e verdes.

Novas funções no *SignalR.StockTicker.js* habilitar e desabilitar os botões com base no estado de mercado e parar ou iniciar a rolagem horizontal do painel eletrônico janela. Uma vez que várias funções que estão sendo adicionadas ao ticker.client, o [jQuery estender a função](http://api.jquery.com/jQuery.extend/) é usado para adicioná-los.

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample31.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>Instalação de cliente adicionais depois de estabelecer a conexão

Depois que o cliente estabelece a conexão, ele tem algum trabalho adicional para fazer: descobrir se o mercado está aberto ou fechado para chamar a função marketClosed ou marketOpened apropriado e anexar as chamadas de método do servidor para os botões.

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample32.js)]

Os métodos de servidor não são vinculei os botões até depois que a conexão é estabelecida, para que o código não é possível tentar chamar os métodos de servidor antes que fiquem disponíveis.

<a id="nextsteps"></a>

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como programar um aplicativo de SignalR que transmite mensagens do servidor para todos os clientes conectados, em intervalos periódicos e em resposta a notificações de qualquer cliente. O padrão de usar uma instância singleton multi-threaded para manter o estado do servidor também pode ser usado também em cenários de jogos online de vários jogadores. Por exemplo, consulte [ShootR jogo baseado em SignalR](https://github.com/NTaylorMullen/ShootR).

Para obter tutoriais que mostram os cenários de comunicação peer-to-peer, consulte [Introdução ao SignalR](index.md) e [a atualização em tempo real com SignalR](index.md).

Para saber mais avançados conceitos de desenvolvimento do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:

- [SignalR do ASP.NET](https://asp.net/signalr/)
- [Projeto de SignalR](http://signalr.net/)
- [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)
- [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)
