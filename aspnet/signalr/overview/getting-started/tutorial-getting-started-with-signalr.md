---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Tutorial: Introdução ao SignalR 2 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como usar o SignalR para criar um aplicativo de chat em tempo real. Você irá adicionar o SignalR para um aplicativo de web ASP.NET vazio e criar um pa HTML...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 3b06e7d0a602e061112adbceba92276f836f6311
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287332"
---
<a name="tutorial-getting-started-with-signalr-2"></a>Tutorial: Introdução ao SignalR 2
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Baixe o projeto concluído](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

> Este tutorial mostra como usar o SignalR para criar um aplicativo de chat em tempo real. Você adicionar o SignalR para um aplicativo de web ASP.NET vazio e criar uma página HTML para enviar e exibir as mensagens. 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - Versão 2 do SignalR
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>Usando o Visual Studio 2012 com este tutorial
> 
> 
> Para usar o Visual Studio 2012 com este tutorial, faça o seguinte:
> 
> - Atualização de seu [Gerenciador de pacotes](http://docs.nuget.org/docs/start-here/installing-nuget) para a versão mais recente.
> - Instalar o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
> - No Web Platform Installer, procure e instale **ASP.NET e Web Tools 2013.1 para Visual Studio 2012**. Isso irá instalar os modelos do Visual Studio para classes do SignalR, como **Hub**.
> - Alguns modelos (como **classe de inicialização OWIN**) não está disponível; nesses casos, use um arquivo de classe em vez disso.
> 
> 
> ## <a name="tutorial-versions"></a>Versões de tutoriais
> 
> Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).
> 
> ## <a name="questions-and-comments"></a>Perguntas e comentários
> 
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Visão geral

Este tutorial apresenta o desenvolvimento do SignalR, mostrando como criar um aplicativo de bate-papo simples baseado em navegador. Você será adicionar a biblioteca do SignalR para um aplicativo de web ASP.NET vazio, crie uma classe de hub para enviar mensagens para os clientes e criar uma página HTML que permite aos usuários enviar e receber mensagens de bate-papo. Para obter um tutorial semelhante que mostra como criar um aplicativo de bate-papo no MVC 5 usando uma exibição do MVC, consulte [Introdução ao SignalR 2 e MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).

> [!NOTE]
> Este tutorial demonstra como criar aplicativos do SignalR na versão 2. Para obter detalhes sobre as alterações entre o SignalR 1.x e 2, consulte [SignalR Atualizando projetos do 1.x](../releases/upgrading-signalr-1x-projects-to-20.md) e [notas de versão do Visual Studio 2013](../../../visual-studio/overview/2013/release-notes.md#TOC13).

O SignalR é uma biblioteca .NET de código-fonte aberto para a criação de aplicativos da web que exigem interação do usuário em tempo real ou atualizações de dados em tempo real. Exemplos incluem aplicativos sociais, jogos multiusuários, clima de notícias e colaboração de negócios ou aplicativos financeiros de atualização. Eles são chamados de aplicativos em tempo real.

O SignalR simplifica o processo de criação de aplicativos em tempo real. Ele inclui uma biblioteca de servidor ASP.NET e uma biblioteca de cliente JavaScript para torná-lo mais fácil de gerenciar conexões de cliente-servidor e enviar por push atualizações de conteúdo aos clientes. Você pode adicionar a biblioteca SignalR para um aplicativo ASP.NET existente para obter a funcionalidade em tempo real.

O tutorial demonstra as seguintes tarefas de desenvolvimento do SignalR:

- Adicionar a biblioteca do SignalR para um aplicativo web ASP.NET.
- Criando uma classe de hub para enviar por push o conteúdo aos clientes.
- Criando uma classe de inicialização OWIN para configurar o aplicativo.
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

Esta seção mostra como usar o Visual Studio 2013 e a versão 2 do SignalR para criar um aplicativo web ASP.NET vazio, adicione o SignalR e criar o aplicativo de bate-papo.

Pré-requisitos:

- Visual Studio 2013. Se você não tiver o Visual Studio, consulte [Downloads do ASP.NET](https://www.asp.net/downloads) para obter o Visual Studio 2013 Express ferramenta de desenvolvimento gratuita.

As etapas a seguir usam o Visual Studio 2013 para criar um aplicativo Web vazio do ASP.NET e adicionar a biblioteca SignalR:

1. No Visual Studio, crie um aplicativo Web ASP.NET.

    ![Criar web](tutorial-getting-started-with-signalr/_static/image2.png)
2. No **novo projeto ASP.NET** janela, deixe **vazia** selecionado e clique em **criar projeto**.

    ![Criar web vazio](tutorial-getting-started-with-signalr/_static/image3.png)
3. Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Classe de Hub do SignalR (v2)**. Nomeie a classe **ChatHub.cs** e adicioná-lo ao projeto. Esta etapa cria o **ChatHub** de classe e o adiciona ao projeto de um conjunto de arquivos de script e referências de assembly que dão suporte ao SignalR.

    > [!NOTE]
    > Você também pode adicionar o SignalR para um projeto, abrindo o **Ferramentas > Gerenciador de pacotes NuGet > Console do Gerenciador de pacotes** e executando um comando:

    `install-package Microsoft.AspNet.SignalR`

    Se você usar o console para adicionar o SignalR, crie a classe de hub do SignalR como uma etapa separada depois de adicionar o SignalR.

    > [!NOTE]
    > Se você estiver usando o Visual Studio 2012, o **classe de Hub do SignalR (v2)** modelo não estará disponível. Você pode adicionar uma sem formatação **classe** chamado `ChatHub` em vez disso.
4. Na **Gerenciador de soluções**, expanda o nó de Scripts. Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.
5. Substitua o código no novo **ChatHub** classe pelo código a seguir.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Classe de inicialização OWIN**. Nomeie a nova classe `Startup` e clique em Okey.

    > [!NOTE]
    > Se você estiver usando o Visual Studio 2012, o **classe de inicialização OWIN** modelo não estará disponível. Você pode adicionar uma sem formatação **classe** chamado `Startup` em vez disso.
7. Altere o conteúdo da nova classe de inicialização para o seguinte.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Página HTML**. Nomeie a nova página `index.html`.
    >[!NOTE]
    >Talvez você precise alterar os números de versão para as referências às bibliotecas do JQuery e SignalR
9. Na **Gerenciador de soluções**, a página HTML que você acabou de criar com o botão direito e clique em **definir como página inicial**.
10. Substitua o código padrão na página HTML com o código a seguir.

    > [!NOTE]
    > Uma versão mais recente dos scripts SignalR pode ser instalada pelo Gerenciador de pacote. Verifique se que as referências de script abaixo correspondem às versões dos arquivos de script no projeto (que será diferente se você tiver adicionado o SignalR usando o NuGet em vez de adicionar um hub.)

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]
11. **Salvar tudo** para o projeto.

<a id="run"></a>

## <a name="run-the-sample"></a>Executar o exemplo

1. Pressione F5 para executar o projeto no modo de depuração. A página HTML carrega em uma instância do navegador e solicitará um nome de usuário.

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr/_static/image4.png)
2. Insira um nome de usuário.
3. Copie a URL da linha de endereço do navegador e usá-lo para abrir duas ou mais instâncias de navegador. Em cada instância do navegador, insira um nome de usuário exclusivo.
4. Em cada instância do navegador, adicione um comentário e clique em **enviar**. Os comentários devem exibir em todas as instâncias do navegador.

    > [!NOTE]
    > Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor. O hub transmite seus comentários para todos os usuários atuais. Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.

    A captura de tela a seguir mostra o aplicativo de bate-papo em execução em três instâncias do navegador, que são atualizados quando uma instância envia uma mensagem:

    ![Navegadores de bate-papo](tutorial-getting-started-with-signalr/_static/image5.png)
5. Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução. Há um arquivo de script denominado **hubs** que a biblioteca SignalR gera dinamicamente em tempo de execução. Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.

    ![](tutorial-getting-started-with-signalr/_static/image6.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Examinar o código

O aplicativo de chat SignalR demonstra duas tarefas de desenvolvimento básicas do SignalR: criar um hub de que o objeto de coordenação principal no servidor e usando a biblioteca jQuery SignalR para enviar e receber mensagens.

### <a name="signalr-hubs"></a>Hubs de SignalR

No exemplo de código a **ChatHub** classe deriva a **ASPNET** classe. Derivando de **Hub** classe é uma maneira útil para criar um aplicativo do SignalR. Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts em uma página da web.

No código de bate-papo, os clientes chamam a **ChatHub.Send** método para enviar uma nova mensagem. O hub por sua vez envia a mensagem a todos os clientes chamando **Clients.All.broadcastMessage**.

O **enviar** método demonstra vários conceitos de hub:

- Declare métodos públicos em um hub para que os clientes possam chamá-los.
- Use o **Microsoft.AspNet.SignalR.Hub.Clients** propriedade dinâmica para acessar todos os clientes conectados a esse hub.
- Chamar uma função no cliente (como o `broadcastMessage` função) para atualizar os clientes.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery"></a>O SignalR e jQuery

A página HTML no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR. As tarefas essenciais no código estão declarando um proxy para o hub, declarar uma função que o servidor pode chamar para enviar conteúdo para clientes e iniciando uma conexão para enviar mensagens para o hub de referência.

O código a seguir declara uma referência a um proxy de hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> Em JavaScript a referência para a classe de servidor e seus membros é em minúsculas concatenadas. O exemplo de código faz referência de c# **ChatHub** classe no JavaScript, enquanto **chatHub**.


O código a seguir é como criar uma função de retorno de chamada no script. A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente. As duas linhas em que o HTML codificar o conteúdo antes de exibi-los são opcionais e mostram uma maneira simples de evitar a injeção de script.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

O código a seguir mostra como abrir uma conexão com o hub. O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página HTML.

> [!NOTE]
> Essa abordagem garante que a conexão seja estabelecida antes de ser executado o manipulador de eventos.


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

<a id="next"></a>

## <a name="next-steps"></a>Próximas etapas

Você aprendeu que o SignalR é uma estrutura para a criação de aplicativos web em tempo real. Você também aprendeu várias tarefas de desenvolvimento do SignalR: como adicionar o SignalR para um aplicativo ASP.NET, como criar uma classe de hub e como enviar e receber mensagens do hub.

Para obter instruções sobre como implantar o aplicativo de exemplo de SignalR no Azure, consulte [usando o SignalR com aplicativos Web no serviço de aplicativo do Azure](../deployment/using-signalr-with-azure-web-sites.md). Para obter informações detalhadas sobre como implantar um projeto de web do Visual Studio para um Site do Windows Azure, consulte [criar um aplicativo web ASP.NET no serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

Para aprender os conceitos mais avançados de desenvolvimentos do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:

- [Projeto de SignalR](http://signalr.net)
- [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)
- [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)
