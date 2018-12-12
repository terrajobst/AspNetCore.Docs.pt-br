---
uid: signalr/overview/getting-started/real-time-web-applications-with-signalr
title: 'Laboratório prático: Aplicativos Web em tempo real com SignalR | Microsoft Docs'
author: rick-anderson
description: A capacidade de conteúdo por push do servidor para os clientes conectados conforme ela ocorre em tempo real de recursos de aplicativos da Web em tempo real. Para desenvolvedores do ASP.NET, ASP...
ms.author: riande
ms.date: 07/16/2014
ms.assetid: ba07958c-42e1-4da0-81db-ba6925ed6db0
msc.legacyurl: /signalr/overview/getting-started/real-time-web-applications-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: de2f2349fc284e167bd8227ae55da79b9f1f4549
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287995"
---
<a name="hands-on-lab-real-time-web-applications-with-signalr"></a>Laboratório prático: Aplicativos Web em tempo real com SignalR
====================

por [Web Camps equipe](https://twitter.com/webcamps)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Baixe o Kit de treinamento do Web Camps](http://aka.ms/webcamps-training-kit)

> A capacidade de conteúdo por push do servidor para os clientes conectados conforme ela ocorre em tempo real de recursos de aplicativos da Web em tempo real. Para desenvolvedores do ASP.NET **SignalR do ASP.NET** é uma biblioteca para adicionar a funcionalidade da web em tempo real aos seus aplicativos. Ela tira proveito de vários transportes, selecionar automaticamente o melhor considerando o cliente e transporte disponíveis melhor do servidor de transporte disponível. Ele aproveita **WebSocket**, uma API de HTML5 que permite a comunicação bidirecional entre o navegador e o servidor.
> 
> **SignalR** também fornece uma API simple e de alto nível para fazer o servidor para cliente RPC (chamada de funções do JavaScript em navegadores de seus clientes com o código do lado do servidor .NET) em seu aplicativo ASP.NET, bem como a adição de ganchos úteis para o gerenciamento de conexão como conectar/desconectar eventos, as conexões de agrupamento e autorização.
> 
> **O SignalR** é uma abstração de alguns dos transportes que são necessários para realizar trabalho em tempo real entre cliente e servidor. Um **SignalR** conexão começa como HTTP e, em seguida, é promovido para um **WebSocket** conexão se estiver disponível. **WebSocket** é o transporte ideal para **SignalR**, pois ele torna o uso mais eficiente da memória do servidor, tem a menor latência e os recursos mais subjacentes (como a comunicação full-duplex entre cliente e servidor), mas ele também tem os requisitos mais rígidos: **WebSocket** exige que o servidor usar **Windows Server 2012** ou **Windows 8**, junto com **.NET Framework 4.5**. Se esses requisitos não forem atendidos, **SignalR** tentará usar outros transportes para fazer suas conexões (como *Ajax sondagem longa*).
> 
> O **SignalR** API contém dois modelos para a comunicação entre clientes e servidores: **Conexões persistentes** e **Hubs**. Um **Conexão** representa um ponto de extremidade simple para enviar um único destinatário, agrupados ou mensagens de difusão. Um **Hub** é um pipeline de mais alto nível construído com a API de Conexão que permite que seu cliente e servidor chamar métodos entre si diretamente.
> 
> ![Arquitetura do SignalR](real-time-web-applications-with-signalr/_static/image1.png)
> 
> Todo o código de exemplo e trechos de código são incluídos no Web Camps treinamento Kit, disponível em [ http://aka.ms/webcamps-training-kit ](http://aka.ms/webcamps-training-kit).


<a id="Overview"></a>
## <a name="overview"></a>Visão geral

<a id="Objectives"></a>
### <a name="objectives"></a>Objetivos

Neste laboratório prático, você aprenderá como:

- Envie notificações do servidor para o cliente usando o SignalR.
- Escalar horizontalmente seu aplicativo SignalR usando **SQL Server**.

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Pré-requisitos

O exemplo a seguir é necessário para concluir este laboratório prático:

- [O Visual Studio Express 2013 para Web](https://www.microsoft.com/visualstudio/) ou maior

<a id="Setup"></a>
### <a name="setup"></a>Configuração

Para executar os exercícios neste laboratório prático, você precisará configurar seu ambiente pela primeira vez.

1. Abra uma janela do Windows Explorer e navegue até o laboratório **origem** pasta.
2. Clique com botão direito **Setup. cmd** e selecione **executar como administrador** para iniciar o processo de instalação que irá configurar seu ambiente e instalem os trechos de código do Visual Studio para este laboratório.
3. Se a caixa de diálogo controle de conta de usuário for mostrada, confirme a ação para continuar.

> [!NOTE]
> Verifique se que você tiver marcado todas as dependências para este laboratório antes de executar a instalação.


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>Usando os trechos de código

Em todo o documento de laboratório, você será instruído a inserir blocos de código. Para sua conveniência, a maioria desse código é fornecido como o Visual Studio trechos de código que pode ser acessada de dentro do Visual Studio 2013 para evitar ter que adicioná-lo manualmente.

> [!NOTE]
> Cada exercício é acompanhado por uma solução inicial localizada na **começar** pasta do exercício que permite que você siga cada exercício independentemente dos outros. Esteja ciente de que os trechos de código são adicionados durante um exercício estão ausentes desses iniciando soluções e podem não funcionar até concluir o exercício. Dentro do código-fonte para um exercício, você também encontrará uma **final** pasta que contém uma solução do Visual Studio com o código que é o resultado de concluir as etapas no exercício correspondente. Você pode usar essas soluções como uma diretriz se você precisar de ajuda adicional ao trabalhar com este laboratório prático.


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>Exercícios

Este laboratório prático inclui os seguintes exercícios:

1. [Trabalhando com dados em tempo real usando SignalR](#Exercise1)
2. [Escala horizontal usando o SQL Server](#Exercise2)

Tempo estimado para concluir este laboratório: **60 minutos**

> [!NOTE]
> Quando você inicia o Visual Studio pela primeira vez, você deve selecionar uma das coleções de configurações predefinidas. Cada coleção predefinida foi projetada para corresponder a um estilo de desenvolvimento específico e determina o comportamento do editor, layouts de janela, trechos de código IntelliSense e opções da caixa de diálogo. Os procedimentos neste laboratório descrevem as ações necessárias para realizar uma determinada tarefa no Visual Studio ao usar o **configurações gerais de desenvolvimento** coleção. Se você escolher uma coleção de configurações diferentes para seu ambiente de desenvolvimento, pode haver diferenças nas etapas que você deve levar em conta.


<a id="Exercise1"></a>
### <a name="exercise-1-working-with-real-time-data-using-signalr"></a>Exercício 1: Trabalhando com dados em tempo real usando SignalR

Embora o bate-papo geralmente é usado como um exemplo, você pode fazer um todo muito mais com a funcionalidade da Web em tempo real. Sempre que um usuário atualiza uma página da web para ver os novos dados ou a página implementa o Ajax longo de sondagem para recuperar novos dados, você pode usar o SignalR.

Dá suporte ao SignalR **por push** ou **difusão** funcionalidade; ele lida com gerenciamento de conexão automaticamente. No clássicas conexões de HTTP para comunicação cliente-servidor, conexão for restabelecida para cada solicitação, mas o SignalR fornece uma conexão persistente entre o cliente e servidor. No SignalR que chama o código do servidor para um código de cliente no navegador usando chamadas de procedimento remoto (RPC), em vez do modelo de solicitação-resposta conhecemos hoje.

Neste exercício, você irá configurar o **Pau teste** aplicativo para usar o SignalR para exibir o painel de estatísticas com as métricas atualizadas sem a necessidade de atualizar a página inteira.

<a id="Ex1Task1"></a>
#### <a name="task-1--exploring-the-geek-quiz-statistics-page"></a>Tarefa 1 – Explorando a página de estatísticas de teste Geek

Nesta tarefa, você percorrer o aplicativo e verificar como a página de estatísticas é mostrada e como você pode melhorar a maneira como as informações é atualizado.

1. Abra **Visual Studio Express 2013 para Web** e abra o **GeekQuiz.sln** solução localizada no **Source\Ex1 WorkingWithRealTimeData\Begin** pasta.
2. Pressione **F5** para executar a solução. O **faça logon no** página deve ser exibida no navegador.

    ![Executar a solução](real-time-web-applications-with-signalr/_static/image2.png "executar a solução")

    *Executar a solução*
3. Clique em **registrar** no canto superior direito da página para criar um novo usuário no aplicativo.

    ![Registre-se o link](real-time-web-applications-with-signalr/_static/image3.png "link registrar")

    *Link de registro*
4. No **registre** , insira um **nome de usuário** e **senha**e, em seguida, clique em **registrar**.

    ![Registrar um usuário](real-time-web-applications-with-signalr/_static/image4.png "registrar um usuário")

    *Registrar um usuário*
5. O aplicativo registra a nova conta e o usuário é autenticado e redirecionado de volta para a home page, mostrando a primeira pergunta de teste.
6. Abra o **estatísticas** página em uma nova janela e colocar o **Home** página e **estatísticas** página lado a lado.

    ![Windows lado a lado](real-time-web-applications-with-signalr/_static/image5.png "pelo windows lado a lado")

    *Windows lado a lado*
7. No **Home** página, responder à pergunta, clicando em uma das opções.

    ![Responder a uma pergunta](real-time-web-applications-with-signalr/_static/image6.png "responder a uma pergunta")

    *Responder a uma pergunta*
8. Depois de clicar em um dos botões, a resposta deverá aparecer.

    ![Pergunta respondida correto](real-time-web-applications-with-signalr/_static/image7.png "pergunta respondida correto")

    *Perguntas respondidas corretamente*
9. Observe que as informações fornecidas na página de estatísticas estão desatualizadas. Atualize a página para ver os resultados atualizados.

    ![Página de estatísticas](real-time-web-applications-with-signalr/_static/image8.png "página de estatísticas")

    *Página de estatísticas*
10. Volte para o Visual Studio e parar a depuração.

<a id="Ex1Task2"></a>
#### <a name="task-2--adding-signalr-to-geek-quiz-to-show-online-charts"></a>Tarefa 2 – adicionando SignalR para teste de Pau para mostrar gráficos Online

Nesta tarefa, você adicionar o SignalR para a solução e enviar atualizações para os clientes automaticamente quando uma nova resposta é enviada ao servidor.

1. Dos **ferramentas** menu no Visual Studio, selecione **Gerenciador de pacotes NuGet**e, em seguida, clique em **Package Manager Console**.
2. No **Package Manager Console** janela, execute o seguinte comando:

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample1.ps1)]

    ![Instalação de pacote do SignalR](real-time-web-applications-with-signalr/_static/image9.png "instalação do pacote de SignalR")

    *Instalação do pacote de SignalR*

   > [!NOTE]
   > Ao instalar **SignalR** versão de pacotes do NuGet 2.0.2 de um aplicativo MVC 5 totalmente novo, você precisará atualizar manualmente **OWIN** pacotes para a versão 2.0.1 (ou superior) antes de instalar o SignalR. Para fazer isso, você pode executar o script a seguir na **Package Manager Console**:
   > 
   > [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample2.ps1)]
   > 
   > Em uma versão futura do SignalR, dependências OWIN serão atualizadas automaticamente.
3. Na **Gerenciador de soluções**, expanda o **Scripts** pasta e observe que o SignalR *js* arquivos foram adicionados à solução.

    ![SignalR JavaScript referencia](real-time-web-applications-with-signalr/_static/image10.png "SignalR JavaScript faz referência")

    *Referências de JavaScript do SignalR*
4. No **Gerenciador de soluções**, com o botão direito do **GeekQuiz** projeto, selecione **Add** | **nova pasta**e nomeie-  **Hubs**.
5. Clique com botão direito do **Hubs** pasta e selecione **adicionar | Novo Item**.

    ![Adicionar novo item](real-time-web-applications-with-signalr/_static/image11.png "adicionar novo item")

    *Adicionar novo item*
6. No **Adicionar Novo Item** caixa de diálogo, selecione o **Visual c# | Web | O SignalR** nó no painel esquerdo, selecione **classe de Hub do SignalR (v2)** do painel central, nomeie o arquivo **StatisticsHub.cs** e clique em **Add**.

    ![Caixa de diálogo Adicionar novo item](real-time-web-applications-with-signalr/_static/image12.png "caixa de diálogo Adicionar novo item")

    *Adicionar caixa de diálogo novo item*
7. Substitua o código na **StatisticsHub** classe pelo código a seguir.

    (Código de trecho de código – *StatisticsHubClass RealTimeSignalR - Ex1 -*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample3.cs)]
8. Abra **Startup.cs** e adicione a seguinte linha no final o **configuração** método.

    (Código de trecho de código – *MapSignalR RealTimeSignalR - Ex1 -*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample4.cs)]
9. Abra o **StatisticsService.cs** página dentro a **serviços** pasta e adicione o seguinte usando diretivas.

    (Código de trecho de código – *UsingDirectives RealTimeSignalR - Ex1 -*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample5.cs)]
10. Para notificar os clientes conectados de atualizações, você primeiro recupere uma **contexto** objeto para a conexão atual. O **Hub** objeto contém métodos para enviar mensagens para um único cliente ou clientes de difusão para todos. Adicione o seguinte método para o **StatisticsService** classe para transmitir os dados de estatísticas.

    (Código de trecho de código – *NotifyUpdatesMethod RealTimeSignalR - Ex1 -*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample6.cs)]

    > [!NOTE]
    > No código acima, você estiver usando um nome de método arbitrário para chamar uma função no cliente (ou seja: *updateStatistics*). O nome do método que você especificar será interpretado como um objeto dinâmico, o que significa que nenhum IntelliSense ou validação de tempo de compilação para ele. A expressão é avaliada em tempo de execução. Quando a chamada de método é executado, o SignalR envia o nome do método e os valores de parâmetro para o cliente. Se o cliente tem um método que corresponde ao nome, esse método é chamado e os valores de parâmetro são passados para ele. Se nenhum método correspondente for encontrado no cliente, nenhum erro será gerado. Para obter mais informações, consulte [guia da API do ASP.NET SignalR Hubs](../guide-to-the-api/hubs-api-guide-server.md).
11. Abra o **TriviaController.cs** página dentro a **controladores** pasta e adicione o seguinte usando diretivas.

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample7.cs)]
12. Adicione o seguinte código realçado para o **Post** método de ação.

    (Código de trecho de código – *NotifyUpdatesCall RealTimeSignalR - Ex1 -*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample8.cs)]
13. Abra o **Statistics.cshtml** página dentro de **modos de exibição | Página inicial** pasta. Localize o **Scripts** seção e adicione as seguintes referências de script no início da seção.

    (Código de trecho de código – *SignalRScriptReferences RealTimeSignalR - Ex1 -*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample9.cshtml)]

    > [!NOTE]
    > Quando você adiciona o SignalR e outras bibliotecas de script ao seu projeto do Visual Studio, o Gerenciador de pacotes poderá instalar uma versão do arquivo de script SignalR é mais recente que a versão mostrada neste tópico. Certifique-se de que a referência de script em seu código corresponda à versão da biblioteca de script instalada em seu projeto.
14. Adicione o seguinte código realçado para se conectar ao cliente para o hub do SignalR e atualizar os dados de estatísticas quando uma nova mensagem é recebida do hub.

    (Código de trecho de código – *SignalRClientCode RealTimeSignalR - Ex1 -*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample10.cshtml)]

    Nesse código, você está criando um Proxy de Hub e registrar um manipulador de eventos para escutar as mensagens enviadas pelo servidor. Nesse caso, você escuta as mensagens enviadas por meio de *updateStatistics* método.

<a id="Ex1Task3"></a>
#### <a name="task-3--running-the-solution"></a>Tarefa 3 – executar a solução

Nesta tarefa, você executará a solução para verificar se o modo de exibição de estatísticas é atualizado automaticamente usando o SignalR depois de responder a uma nova pergunta.

1. Pressione **F5** para executar a solução.

    > [!NOTE]
    > Se ainda não estiver conectado ao aplicativo, faça logon com o usuário que você criou na tarefa 1.
2. Abra o **estatísticas** página em uma nova janela e colocar o **Home** página e **estatísticas** página lado a lado, como você fez na tarefa 1.
3. No **Home** página, responder à pergunta, clicando em uma das opções.

    ![Respondendo a outra pergunta](real-time-web-applications-with-signalr/_static/image13.png "responder outra pergunta")

    *Respondendo a outra pergunta*
4. Depois de clicar em um dos botões, a resposta deverá aparecer. Observe que as informações de estatísticas na página são atualizadas automaticamente depois de responder à pergunta com as informações atualizadas sem a necessidade de atualizar a página inteira.

    ![Página de estatísticas atualizado após a resposta](real-time-web-applications-with-signalr/_static/image14.png "página estatísticas atualizadas após a resposta")

    *Página de estatísticas atualizada após a resposta*

<a id="Exercise2"></a>
### <a name="exercise-2-scaling-out-using-sql-server"></a>Exercício 2: Escala horizontal usando o SQL Server

Ao dimensionar um aplicativo web, geralmente você pode escolher entre *escalar verticalmente* e *escalando horizontalmente* opções. *Escalar verticalmente* significa usar um servidor maior, com mais recursos (CPU, RAM, etc.) enquanto *escalar horizontalmente* significa adicionar mais servidores para lidar com a carga. O problema com o último é que os clientes podem obter roteados para diferentes servidores. Um cliente que está conectado a um servidor não receberá as mensagens enviadas do outro servidor.

Você pode resolver esses problemas por meio de um componente chamado *backplane*para encaminhar mensagens entre servidores. Com um backplane habilitado, cada instância do aplicativo envia mensagens ao backplane e backplane encaminha-os para as outras instâncias do aplicativo.

Atualmente, há três tipos de backplanes para SignalR:

- **Windows Azure Service Bus**. O barramento de serviço é uma infraestrutura de mensagens que permite que os componentes enviar mensagens de acoplamento fraco.
- **SQL Server**. Backplane do SQL Server grava mensagens em tabelas SQL. Backplane usa o Service Broker para o sistema de mensagens eficiente. No entanto, ele também funciona se o Service Broker não está habilitado.
- **Redis**. O redis é um repositório de chave-valor na memória. Redis oferece suporte a um padrão de publicação/assinatura ("pub/sub") para enviar mensagens.

Cada mensagem é enviada por meio de um barramento de mensagem. Um barramento de mensagem implementa o [IMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) interface, que fornece uma abstração de publicação/assinatura. Os backplanes funcionam, substituindo o padrão **IMessageBus** com barramento projetado por esse backplane.

Cada instância de servidor se conecta ao backplane por meio do barramento. Quando uma mensagem é enviada, ela vai para o backplane e o backplane envia para cada servidor. Quando um servidor recebe uma mensagem do backplane, ele armazena a mensagem em seu cache local. O servidor, em seguida, entrega mensagens para os clientes de seu cache local.

Para obter mais informações sobre como funciona o backplane SignalR, leia esta [artigo](../performance/scaleout-in-signalr.md).

> [!NOTE]
> Existem alguns cenários onde um backplane pode se tornar um gargalo. Aqui estão alguns cenários típicos do SignalR:
> 
> - [Servidor de transmissão](tutorial-server-broadcast-with-signalr.md) (por exemplo, bolsa): Backplanes funcionam bem para este cenário, porque o servidor controla a taxa na qual as mensagens são enviadas.
> - [Cliente-para-cliente](tutorial-getting-started-with-signalr.md) (por exemplo, bate-papo): Nesse cenário, o backplane pode ser um gargalo se o número de mensagens pode ser dimensionado com o número de clientes. ou seja, se a taxa de mensagens aumenta proporcionalmente de mais clientes unir.
> - [Em tempo real de alta frequência](tutorial-high-frequency-realtime-with-signalr.md) (por exemplo, jogos em tempo real): Um backplane não é recomendado para este cenário.


Neste exercício, você usará **SQL Server** para distribuir mensagens entre o **Pau teste** aplicativo. Você executará essas tarefas em um computador de teste único para aprender a definir a configuração, mas para obter o efeito completo, você precisará implantar o aplicativo do SignalR em dois ou mais servidores. Você também deve instalar o SQL Server em um dos servidores, ou em um servidor dedicado separado.

![Escale horizontalmente usando o diagrama de SQL Server](real-time-web-applications-with-signalr/_static/image15.png)

<a id="Ex2Task1"></a>
#### <a name="task-1---understanding-the-scenario"></a>Tarefa 1: Noções básicas sobre o cenário

Nesta tarefa, você executará 2 instâncias de **Pau teste** simular IIS de várias instâncias no seu computador local. Nesse cenário, ao responder às perguntas de desafios em um aplicativo, a atualização não será notificada na página de estatísticas da segunda instância. Essa simulação é semelhante a um ambiente onde o aplicativo é implantado em várias instâncias e usando um balanceador de carga para se comunicar com eles.

1. Abra o **Begin.sln** solução localizada na **origem/o Ex2-ScalingOutWithSQLServer/início** pasta. Uma vez carregado, você perceberá na **Gerenciador de servidores** que a solução tem dois projetos com idênticos, mas diferentes nomes de estruturas. Isso será simular que executa duas instâncias do mesmo aplicativo em seu computador local.

    ![Começar a solução de simulação de 2 instâncias de teste de Pau](real-time-web-applications-with-signalr/_static/image16.png "começar a solução de simulação de 2 instâncias de teste de pau")

    *Começar a solução de simulação de 2 instâncias de teste de pau*
2. Abra a página de propriedades da solução clicando duas vezes no nó da solução e selecionando **propriedades**. Sob **projeto de inicialização**, selecione **vários projetos de inicialização** e altere a **ação** valor para ambos os projetos para *iniciar*.

    ![Iniciando vários projetos](real-time-web-applications-with-signalr/_static/image17.png "iniciando vários projetos")

    *Iniciando vários projetos*
3. Pressione **F5** para executar a solução. O aplicativo será iniciado de duas instâncias de **Pau teste** em portas diferentes, simulando várias instâncias do mesmo aplicativo. Fixe um dos navegadores à esquerda e o outro à direita da tela. Faça logon com suas credenciais ou registre um novo usuário. Depois de conectado, mantenha a página de desafio à esquerda e vá para o **estatísticas** página no navegador à direita.

    ![Teste de Pau lado a lado](real-time-web-applications-with-signalr/_static/image18.png)

    *Teste de Pau lado a lado*

    ![Teste de Geek em portas diferentes](real-time-web-applications-with-signalr/_static/image19.png)

    *Teste de Geek em portas diferentes*
4. Começar respondendo perguntas no navegador à esquerda e você observará que o **estatísticas** página no navegador à direita não está sendo atualizado. Isso ocorre porque **SignalR** usa um local de cache para distribuir mensagens através de seus clientes e esse cenário está simulando várias instâncias, portanto, o cache não é compartilhado entre eles. Você pode verificar se **SignalR** está funcionando, teste as mesmas etapas, mas usando um único aplicativo. As tarefas a seguir, você configurará um backplane para replicar as mensagens entre instâncias.
5. Volte para o Visual Studio e parar a depuração.

<a id="Ex2Task2"></a>
#### <a name="task-2--creating-the-sql-server-backplane"></a>Tarefa 2 – criar o Backplane do SQL Server

Nesta tarefa, você criará um banco de dados que servirá como um backplane para o **Pau teste** aplicativo. Você usará **Pesquisador de objetos do SQL Server** procurar seu servidor e inicializar o banco de dados. Além disso, você habilitará o **Service Broker**.

1. Na **Visual Studio**, abra menu **exibição** e selecione **Pesquisador de objetos do SQL Server**.
2. Conectar-se à instância do LocalDB clicando com o **SQL Server** nó e selecionando **adicionar SQL Server...**  opção.

    ![Adição de uma instância do SQL Server](real-time-web-applications-with-signalr/_static/image20.png "adicionando uma instância do SQL Server")

    *A adição de uma instância do SQL Server para o Pesquisador de objetos do SQL Server*
3. Defina o **nome do servidor** para *(localdb) \v11.0* e deixe **autenticação do Windows** como seu modo de autenticação. Clique em **Connect** para continuar.

    ![Conectar-se ao LocalDB](real-time-web-applications-with-signalr/_static/image21.png "conectar-se ao LocalDB")

    *Conectar-se ao LocalDB*
4. Agora que você está conectado à sua instância de LocalDB, você precisará criar um banco de dados que representará o backplane do SQL Server para o SignalR. Para fazer isso, clique com botão direito do **bancos de dados** nó e selecione **adicionar novo banco de dados**.

    ![Adicionando um novo banco de dados](real-time-web-applications-with-signalr/_static/image22.png "adicionando um novo banco de dados")

    *Adicionando um novo banco de dados*
5. Defina o nome do banco de dados como *SignalR* e clique em **Okey** para criá-lo.

    ![Criando o banco de dados do SignalR](real-time-web-applications-with-signalr/_static/image23.png "criando o banco de dados do SignalR")

    *Criando o banco de dados do SignalR*

    > [!NOTE]
    > Você pode escolher qualquer nome para o banco de dados.
6. Para receber atualizações com mais eficiência do backplane, é recomendável habilitar o Service Broker para o banco de dados. O Service Broker fornece suporte nativo para mensagens e enfileiramento no SQL Server. Backplane também funciona sem o Service Broker. Abra uma nova consulta clicando com o banco de dados e selecione **nova consulta**.

    ![Abrir uma nova consulta](real-time-web-applications-with-signalr/_static/image24.png "abrindo uma nova consulta")

    *Abrir uma nova consulta*
7. Para verificar se o Service Broker está habilitado, consulte o **está\_broker\_habilitada** coluna no **sys. Databases** exibição do catálogo. Execute o seguinte script na janela de consulta abertos recentemente.

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample11.sql)]

    ![Consultar o Status do Service Broker](real-time-web-applications-with-signalr/_static/image25.png "consultar o Status do Service Broker")

    *Consultar o Status do Service Broker*
8. Se o valor da **está\_broker\_habilitada** coluna em seu banco de dados é &quot;0&quot;, use o seguinte comando para habilitá-lo. Substitua **&lt;seu banco de dados&gt;** com o nome definido ao criar o banco de dados (por exemplo: SignalR).

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample12.sql)]

    ![Habilitando o Service Broker](real-time-web-applications-with-signalr/_static/image26.png "habilitando o Service Broker")

    *Habilitando o Service Broker*

    > [!NOTE]
    > Se essa consulta é exibido um deadlock, certifique-se não há nenhum aplicativo conectado ao banco de dados.

<a id="Ex2Task3"></a>
#### <a name="task-3--configuring-the-signalr-application"></a>Tarefa 3 – configurar o aplicativo de SignalR

Nesta tarefa, você configurará **Pau teste** para se conectar ao backplane do SQL Server. Primeiro, você irá adicionar o **SignalR.SqlServer** pacote NuGet e a conexão do conjunto de cadeia de caracteres para seu banco de dados do backplane.

1. Abra o **Package Manager Console** de **ferramentas** > **NuGet Package Manager**. Certifique-se de que **GeekQuiz** projeto for selecionado na **projeto padrão** lista suspensa. Digite o seguinte comando para instalar o **Microsoft.AspNet.SignalR.SqlServer** pacote do NuGet.

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample13.ps1)]
2. Repita a etapa anterior, mas desta vez para o projeto **GeekQuiz2**.
3. Para configurar o backplane do SQL Server, abra o **Startup.cs** arquivo da **GeekQuiz** do projeto e adicione o seguinte código para o **configurar** método. Substitua **&lt;seu banco de dados&gt;** com seu nome de banco de dados que você usou ao criar o backplane do SQL Server. Repita essa etapa para o **GeekQuiz2** projeto.

    (Código de trecho de código – *StartupConfiguration RealTimeSignalR - o Ex2 -*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample14.cs)]
4. Agora que ambos os projetos são configurados para usar o backplane do SQL Server, pressione **F5** executá-los simultaneamente.
5. Novamente, **Visual Studio** iniciará duas instâncias do **Pau teste** em portas diferentes. Fixar um dos navegadores à esquerda e o outro à direita da tela e faça logon com suas credenciais. Mantenha a página de desafio à esquerda e vá para **estatísticas** pagein navegador à direita.
6. Começar a responder às perguntas no navegador à esquerda. Neste momento, o **estatísticas** página é atualizada, graças ao backplane. Alternar entre aplicativos (**estatísticas** agora está à esquerda, e **Trívia** está à direita) e repita o teste para validar se ele está funcionando para ambas as instâncias. Backplane serve como uma *cache compartilhado do* de mensagens para cada servidor conectado e cada servidor armazena as mensagens em seu próprio cache local para distribuir aos clientes conectados.
7. Volte para o Visual Studio e parar a depuração.
8. O componente de backplane do SQL Server gera automaticamente as tabelas necessárias no banco de dados especificado. No **Pesquisador de objetos do SQL Server** painel, abra o banco de dados que você criou para o backplane (por exemplo: SignalR) e expanda suas tabelas. Você deve ver as tabelas a seguir:

    ![Backplane gerado tabelas](real-time-web-applications-with-signalr/_static/image27.png)

    *Backplane gerado tabelas*
9. Clique com botão direito do **SignalR.Messages\_0** da tabela e selecione **exibir dados**.

    ![Exibir tabela de mensagens do SignalR Backplane](real-time-web-applications-with-signalr/_static/image28.png)

    *Exibir tabela de mensagens do SignalR Backplane*
10. Você pode ver as diferentes mensagens enviadas para o **Hub** ao responder as perguntas de desafios. Backplane distribui essas mensagens para qualquer instância conectada.

    ![Tabela de mensagens do backplane](real-time-web-applications-with-signalr/_static/image29.png)

    *Tabela de mensagens do backplane*

* * *

<a id="Summary"></a>
## <a name="summary"></a>Resumo

Neste laboratório prático, você aprendeu a adicionar **SignalR** para seu aplicativo e enviar notificações do servidor para seus clientes conectados usando **Hubs**. Além disso, você aprendeu a expandir seu aplicativo usando um *backplane* componente quando o aplicativo for implantado em várias instâncias do IIS.
