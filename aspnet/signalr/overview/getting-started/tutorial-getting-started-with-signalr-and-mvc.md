---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Tutorial: Bate-papo em tempo real com SignalR 2 e MVC 5 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real. Adicionar o SignalR para um aplicativo MVC 5.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: eb4b7e1403f4070d65702b756bf98c5294c7fb17
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098598"
---
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a>Tutorial: Bate-papo em tempo real com SignalR 2 e MVC 5

Este tutorial mostra como usar o ASP.NET SignalR 2 para criar um aplicativo de bate-papo em tempo real. Adicionar o SignalR para um aplicativo MVC 5 e criar uma exibição de bate-papo para enviar e exibir as mensagens.

Neste tutorial, você:

> [!div class="checklist"]
> * Configurar o projeto
> * Executar o exemplo
> * Examinar o código

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) com o **ASP.NET e desenvolvimento web** carga de trabalho.

## <a name="set-up-the-project"></a>Configurar o projeto

Esta seção mostra como usar o Visual Studio 2017 e SignalR 2 para criar um aplicativo ASP.NET MVC 5 vazio, adicione a biblioteca SignalR e criar o aplicativo de bate-papo.

1. No Visual Studio, crie um aplicativo c# ASP.NET que tem como alvo o .NET Framework 4.5, nomeie-o SignalRChat e clique em Okey.

    ![Criar web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. Na **novo aplicativo de Web do ASP.NET - SignalRMvcChat**, selecione **MVC** e, em seguida, selecione **alterar autenticação**.

1. Na **alterar autenticação**, selecione **sem autenticação** e clique em **Okey**.

    ![Selecione sem autenticação](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. Na **novo aplicativo de Web do ASP.NET - SignalRMvcChat**, selecione **Okey**.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.

1. Na **Adicionar Novo Item – SignalRChat**, selecione **instalado** > **Visual C#**   >  **Web**  >  **SignalR** e, em seguida, selecione **classe de Hub do SignalR (v2)**.

1. Nomeie a classe *ChatHub* e adicioná-lo ao projeto.

    Esta etapa cria o *ChatHub.cs* arquivo de classe e adiciona um conjunto de arquivos de script e referências de assembly que dão suporte ao SignalR ao projeto.

1. Substitua o código no novo *ChatHub.cs* arquivo de classe com este código:

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **classe**.

1. Nomeie a nova classe *inicialização* e adicioná-lo ao projeto.

1. Substitua o código na *Startup.cs* arquivo de classe com este código:

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. Na **Gerenciador de soluções**, selecione **controladores** > **HomeController.cs**.

1. Adicione este método para o *HomeController.cs*.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    Esse método retorna o **bate-papo** exibição que você cria em uma etapa posterior.

1. Na **Gerenciador de soluções**, clique com botão direito **exibições** > **página inicial**e selecione **adicionar**  >    **Modo de exibição**.

1. Na **adicionar exibição**, nomeie a nova exibição **bate-papo** e selecione **adicionar**.

1. Substitua o conteúdo do **Chat.cshtml** com este código:

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. Na **Gerenciador de soluções**, expanda **Scripts**.

    Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.

    > [!IMPORTANT]
    > O Gerenciador de pacotes pode ter instalado uma versão mais recente dos scripts do SignalR.

1. Verifique se as referências de script no bloco de código correspondem às versões dos arquivos de script no projeto.

    Referências de script de bloco de código original:

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. Se eles não corresponderem, atualize o *. cshtml* arquivo.

1. Na barra de menus, selecione **arquivo** > **Salvar tudo**.

## <a name="run-the-sample"></a>Executar o exemplo

1. Na barra de ferramentas, ative **depuração de Script** e, em seguida, selecione o botão Reproduzir para executar o exemplo no modo de depuração.

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. Quando o navegador for aberta, insira um nome para sua identidade de bate-papo.

1. Copie a URL do navegador, abra dois outros navegadores e cole as URLs em barras de endereço.

1. Em cada navegador, insira um nome exclusivo.

1. Agora, adicione um comentário e selecione **enviar**. Repita que em outros navegadores. Os comentários são exibidos em tempo real.

    > [!NOTE]
    > Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor. O hub transmite seus comentários para todos os usuários atuais. Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.

    Veja como o aplicativo de bate-papo é executado em três diferentes navegadores. Quando o Tom, Anand e Susan enviam mensagens, todos os navegadores são atualizados em tempo real:

    ![Todos os três navegadores exibem ao mesmo histórico de bate-papo](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução. Há um arquivo de script denominado *hubs* que a biblioteca SignalR gera em tempo de execução. Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.

    ![script de hubs gerado automaticamente no nó documentos de Script](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a>Examinar o código

O aplicativo de chat SignalR demonstra duas tarefas básicas de desenvolvimento de SignalR. Ele mostra como criar um hub. O servidor usa esse hub de que o objeto principal de coordenação. O hub usa a biblioteca jQuery SignalR para enviar e receber mensagens.

### <a name="signalr-hubs-in-the-chathubcs"></a>Hubs de SignalR no ChatHub.cs

No exemplo de código, o `ChatHub` classe deriva de `Microsoft.AspNet.SignalR.Hub` classe. Derivando a `Hub` classe é uma maneira útil para criar um aplicativo do SignalR. Você pode criar métodos públicos em sua classe de hub e, em seguida, acessar esses métodos chamando-as de scripts em uma página da web.

No código de bate-papo, os clientes chamam o `ChatHub.Send` método para enviar uma nova mensagem. O hub por sua vez envia a mensagem a todos os clientes chamando `Clients.All.addNewMessageToPage`.

O `Send` método demonstra vários conceitos de hub:

* Declare métodos públicos em um hub para que os clientes possam chamá-los.

* Use o `Microsoft.AspNet.SignalR.Hub.Clients` propriedade dinâmica para se comunicar com todos os clientes conectados a esse hub.

* Chamar uma função no cliente (como o `addNewMessageToPage` função) para atualizar os clientes.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a>O SignalR e jQuery Chat.cshtml

O *Chat.cshtml* arquivo de exibição no código de exemplo mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR.  O código realiza várias tarefas importantes. Cria uma referência para o proxy gerado automaticamente para o hub, ele declara uma função que o servidor pode chamar para enviar por push o conteúdo para clientes, e ele inicia uma conexão para enviar mensagens ao hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> No JavaScript, a referência para a classe de servidor e seus membros está em camelCase. As referências de exemplo de código a C# `ChatHub` classe no JavaScript, enquanto `chatHub`.

Este bloco de código, você cria uma função de retorno de chamada no script.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente. A chamada opcional para o `htmlEncode` função mostra uma maneira para HTML codificar o conteúdo da mensagem antes de exibi-la na página. É uma maneira de evitar a injeção de script.

Esse código abre uma conexão com o hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> Essa abordagem garante que você estabeleça uma conexão antes de ser executado o manipulador de eventos.

O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página de bate-papo.

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o SignalR, consulte os seguintes recursos:

* [Projeto de SignalR](http://signalr.net)

* [GitHub do SignalR e exemplos](https://github.com/SignalR/SignalR)

* [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Configurar o projeto
> * Executar a amostra
> * Examinado o código

Avance para o próximo artigo para saber como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer a funcionalidade de mensagens de alta frequência.
> [!div class="nextstepaction"]
> [Aplicativo Web com alta frequência de mensagens](tutorial-high-frequency-realtime-with-signalr.md)