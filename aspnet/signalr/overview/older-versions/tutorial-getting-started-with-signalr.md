---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: 'Tutorial: Introdução ao SignalR 1.x | Microsoft Docs'
author: pfletcher
description: Use o ASP.NET SignalR para criar um aplicativo de bate-papo em tempo real em uma página HTML.
ms.author: riande
ms.date: 02/18/2013
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 720a4879f5fbe3c0c2b4c7809cb94c22547329c3
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287345"
---
<a name="tutorial-getting-started-with-signalr-1x"></a>Tutorial: Introdução ao SignalR 1.x
====================
por [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tutorial mostra como usar o SignalR para criar um aplicativo de chat em tempo real. Você adicionar o SignalR para um aplicativo de web ASP.NET vazio e criar uma página HTML para enviar e exibir as mensagens.


## <a name="overview"></a>Visão geral

Este tutorial apresenta o desenvolvimento do SignalR, mostrando como criar um aplicativo de bate-papo simples baseado em navegador. Você será adicionar a biblioteca do SignalR para um aplicativo de web ASP.NET vazio, crie uma classe de hub para enviar mensagens para os clientes e criar uma página HTML que permite aos usuários enviar e receber mensagens de bate-papo. Para obter um tutorial semelhante que mostra como criar um aplicativo de bate-papo no MVC 4 usando uma exibição do MVC, consulte [Introdução ao SignalR e MVC 4](index.md).

> [!NOTE]
> Este tutorial usa a versão de lançamento (1.x) do SignalR. Para obter detalhes sobre as alterações entre o SignalR 1.x e 2.0, consulte [atualizando SignalR 1.x projetos](../releases/upgrading-signalr-1x-projects-to-20.md).

O SignalR é uma biblioteca .NET de código-fonte aberto para a criação de aplicativos da web que exigem interação do usuário em tempo real ou atualizações de dados em tempo real. Exemplos incluem aplicativos sociais, jogos multiusuários, clima de notícias e colaboração de negócios ou aplicativos financeiros de atualização. Eles são chamados de aplicativos em tempo real.

O SignalR simplifica o processo de criação de aplicativos em tempo real. Ele inclui uma biblioteca de servidor ASP.NET e uma biblioteca de cliente JavaScript para torná-lo mais fácil de gerenciar conexões de cliente-servidor e enviar por push atualizações de conteúdo aos clientes. Você pode adicionar a biblioteca SignalR para um aplicativo ASP.NET existente para obter a funcionalidade em tempo real.

O tutorial demonstra as seguintes tarefas de desenvolvimento do SignalR:

- Adicionar a biblioteca do SignalR para um aplicativo web ASP.NET.
- Criando uma classe de hub para enviar por push o conteúdo aos clientes.
- Usando a biblioteca jQuery SignalR em uma página da web para enviar mensagens e exibir as atualizações do hub.

A captura de tela a seguir mostra o aplicativo de bate-papo em execução em um navegador. Cada novo usuário pode postar comentários e ver os comentários adicionados depois que o usuário se associe o bate-papo.

![Instâncias de bate-papo](tutorial-getting-started-with-signalr/_static/image1.png)

Seções:

- [Configurar o projeto](#setup)
- [Executar o exemplo](#run)
- [Examinar o código](#code)
- [Próximas Etapas](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Configurar o projeto

Esta seção mostra como criar um aplicativo web ASP.NET vazio, adicione o SignalR e criar o aplicativo de bate-papo.

Pré-requisitos:

- Visual Studio 2010 SP1 ou 2012. Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2012 Express ferramenta de desenvolvimento gratuita.
- [Microsoft ASP.NET e Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941). Para o Visual Studio 2012, o instalador adiciona novos recursos do ASP.NET, incluindo modelos de SignalR ao Visual Studio. Para o Visual Studio 2010 SP1, um instalador não está disponível, mas você pode concluir o tutorial instalando o pacote NuGet do SignalR, conforme descrito nas etapas de configuração.

As etapas a seguir usam o Visual Studio 2012 para criar um aplicativo Web vazio do ASP.NET e adicionar a biblioteca SignalR:

1. No Visual Studio, crie um aplicativo Web vazio do ASP.NET.

    ![Criar web vazio](tutorial-getting-started-with-signalr/_static/image2.png)
2. Abra o **Package Manager Console** selecionando **ferramentas | Gerenciador de pacotes NuGet | Package Manager Console**. Digite o seguinte comando na janela de console:

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    Este comando instala a versão mais recente do SignalR 1.x.
3. Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Classe**. Nomeie a nova classe **ChatHub**.
4. Na **Gerenciador de soluções** expanda o nó de Scripts. Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.

    ![Referências da biblioteca](tutorial-getting-started-with-signalr/_static/image3.png)
5. Substitua o código na **ChatHub** classe pelo código a seguir.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Novo Item**. No **Adicionar Novo Item** caixa de diálogo, selecione **classe de aplicativo Global** e clique em **adicionar**.

    ![Adicionar global](tutorial-getting-started-with-signalr/_static/image4.png)
7. Adicione o seguinte `using` instruções após fornecido `using` instruções na classe Global.asax.cs.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. Adicione a seguinte linha de código no `Application_Start` método da classe Global para registrar a rota padrão para hubs do SignalR.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Novo Item**. No **Adicionar Novo Item** caixa de diálogo, selecione Página Html e clique em **Add**.
10. Na **Gerenciador de soluções**, a página HTML que você acabou de criar com o botão direito e clique em **definir como página inicial**.
11. Substitua o código padrão na página HTML com o código a seguir.

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. **Salvar tudo** para o projeto.

<a id="run"></a>

## <a name="run-the-sample"></a>Executar o exemplo

1. Pressione F5 para executar o projeto no modo de depuração. A página HTML carrega em uma instância do navegador e solicitará um nome de usuário.

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr/_static/image5.png)
2. Insira um nome de usuário.
3. Copie a URL da linha de endereço do navegador e usá-lo para abrir duas ou mais instâncias de navegador. Em cada instância do navegador, insira um nome de usuário exclusivo.
4. Em cada instância do navegador, adicione um comentário e clique em **enviar**. Os comentários devem exibir em todas as instâncias do navegador.

    > [!NOTE]
    > Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor. O hub transmite seus comentários para todos os usuários atuais. Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.

    A captura de tela a seguir mostra o aplicativo de bate-papo em execução em três instâncias do navegador, que são atualizados quando uma instância envia uma mensagem:

    ![Navegadores de bate-papo](tutorial-getting-started-with-signalr/_static/image6.png)
5. Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução. Há um arquivo de script denominado **hubs** que a biblioteca SignalR gera dinamicamente em tempo de execução. Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.

    ![Script gerado hub](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Examinar o código

O aplicativo de chat SignalR demonstra duas tarefas de desenvolvimento básicas do SignalR: criar um hub de que o objeto de coordenação principal no servidor e usando a biblioteca jQuery SignalR para enviar e receber mensagens.

### <a name="signalr-hubs"></a>Hubs de SignalR

No exemplo de código a **ChatHub** classe deriva a **ASPNET** classe. Derivando de **Hub** classe é uma maneira útil para criar um aplicativo do SignalR. Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts do jQuery em uma página da web.

No código de bate-papo, os clientes chamam a **ChatHub.Send** método para enviar uma nova mensagem. O hub por sua vez envia a mensagem a todos os clientes chamando **Clients.All.broadcastMessage**.

O **enviar** método demonstra vários conceitos de hub:

- Declare métodos públicos em um hub para que os clientes possam chamá-los.
- Use o **Microsoft.AspNet.SignalR.Hub.Clients** propriedade dinâmica para acessar todos os clientes conectados a esse hub.
- Chamar uma função do jQuery no cliente (como o `broadcastMessage` função) para atualizar os clientes.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>O SignalR e jQuery

A página HTML no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR. As tarefas essenciais no código estão declarando um proxy para o hub, declarar uma função que o servidor pode chamar para enviar conteúdo para clientes e iniciando uma conexão para enviar mensagens para o hub de referência.

O código a seguir declara um proxy para um hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> No jQuery a referência para a classe de servidor e seus membros é em minúsculas concatenadas. O exemplo de código faz referência de c# **ChatHub** classe no jQuery como **chatHub**.


O código a seguir é como criar uma função de retorno de chamada no script. A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente. As duas linhas em que o HTML codificar o conteúdo antes de exibi-los são opcionais e mostram uma maneira simples de evitar a injeção de script.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

O código a seguir mostra como abrir uma conexão com o hub. O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página HTML.

> [!NOTE]
> Essa abordagem garante que a conexão seja estabelecida antes de ser executado o manipulador de eventos.


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Próximas etapas

Você aprendeu que o SignalR é uma estrutura para a criação de aplicativos web em tempo real. Você também aprendeu várias tarefas de desenvolvimento do SignalR: como adicionar o SignalR para um aplicativo ASP.NET, como criar uma classe de hub e como enviar e receber mensagens do hub.

Você pode tornar o aplicativo de exemplo neste tutorial ou outros aplicativos do SignalR disponíveis na Internet por implantá-los para um provedor de hospedagem. A Microsoft oferece a hospedagem de web gratuita para até 10 sites em um livre [conta de avaliação do Windows Azure](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604). Para obter instruções sobre como implantar o aplicativo de exemplo SignalR, consulte [publicar o SignalR obtendo introdução de exemplo como um Site do Windows Azure](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx). Para obter informações detalhadas sobre como implantar um projeto de web do Visual Studio para um Site do Windows Azure, consulte [Implantando um aplicativo ASP.NET a um Site do Windows Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet). (Observação: O transporte de WebSocket atualmente não há suporte para o Windows Azure Web Sites. O transporte de WebSocket quando não estiver disponível, o SignalR usa os outros transportes disponíveis, conforme descrito na seção de transportes a [Introdução ao SignalR tópico](index.md).)

Para aprender os conceitos mais avançados de desenvolvimentos do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:

- [Projeto de SignalR](http://signalr.net)
- [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)
- [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)
