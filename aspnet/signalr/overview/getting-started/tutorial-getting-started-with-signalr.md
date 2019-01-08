---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Tutorial: Bate-papo em tempo real com SignalR 2 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como usar o SignalR para criar um aplicativo de chat em tempo real. Adicionar o SignalR para um aplicativo de web ASP.NET vazio.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: aa015abc47bb2450e04e167c0404aaa1d119ba2c
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098618"
---
# <a name="tutorial-real-time-chat-with-signalr-2"></a>Tutorial: Bate-papo em tempo real com SignalR 2

Este tutorial mostra como usar o SignalR para criar um aplicativo de bate-papo em tempo real. Adicionar o SignalR para um aplicativo de web ASP.NET vazio e criar uma página HTML para enviar e exibir as mensagens.

Neste tutorial, você:

> [!div class="checklist"]
> * Configurar o projeto
> * Executar o exemplo
> * Examinar o código

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) com o **ASP.NET e desenvolvimento web** carga de trabalho.

## <a name="set-up-the-project"></a>Configurar o projeto

Esta seção mostra como usar o Visual Studio 2017 e SignalR 2 para criar um aplicativo web ASP.NET vazio, adicione o SignalR e criar o aplicativo de bate-papo.

1. No Visual Studio, crie um aplicativo Web ASP.NET.

    ![Criar web](tutorial-getting-started-with-signalr/_static/image2.png)

1. No **novo projeto ASP.NET - SignalRChat** janela, deixe **vazia** selecionado e selecione **Okey**.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.

1. Na **Adicionar Novo Item – SignalRChat**, selecione **instalado** > **Visual C#**   >  **Web**  >  **SignalR** e, em seguida, selecione **classe de Hub do SignalR (v2)**.

1. Nomeie a classe *ChatHub* e adicioná-lo ao projeto.

    Esta etapa cria o *ChatHub.cs* arquivo de classe e adiciona um conjunto de arquivos de script e referências de assembly que dão suporte ao SignalR ao projeto.

1. Substitua o código no novo *ChatHub.cs* arquivo de classe com este código:

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.

1. Na **Adicionar Novo Item – SignalRChat** selecionar **instalado** > **Visual C#**   >  **Web** e, em seguida, Selecione **classe de inicialização OWIN**.

1. Nomeie a classe *inicialização* e adicioná-lo ao projeto.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **página HTML**.

1. Nomeie a nova página *índice* e selecione **Okey**.

1. Na **Gerenciador de soluções**, a página HTML que você criou com o botão direito e selecione **definir como página inicial**.

1. Substitua o código padrão na página HTML com este código:

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]

1. Na **Gerenciador de soluções**, expanda **Scripts**.

    Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.

    > [!IMPORTANT]
    > O Gerenciador de pacotes pode ter instalado uma versão mais recente dos scripts do SignalR.

1. Verifique se as referências de script no bloco de código correspondem às versões dos arquivos de script no projeto.

    Referências de script de bloco de código original:
    ```html
    <!--Script references. -->
    <!--Reference the jQuery library. -->
    <script src="Scripts/jquery-3.1.1.min.js" ></script>
    <!--Reference the SignalR library. -->
    <script src="Scripts/jquery.signalR-2.2.1.min.js"></script>
    ```

1. Se eles não corresponderem, atualize o *. HTML* arquivo.

1. Na barra de menus, selecione **arquivo** > **Salvar tudo**.

## <a name="run-the-sample"></a>Executar o exemplo

1. Na barra de ferramentas, ative **depuração de Script** e, em seguida, selecione o botão Reproduzir para executar o exemplo no modo de depuração.

    ![Insira o nome de usuário](tutorial-getting-started-with-signalr/_static/image3.png)

1. Quando o navegador for aberta, insira um nome para sua identidade de bate-papo.

1. Copie a URL do navegador, abra dois outros navegadores e cole as URLs em barras de endereço.

1. Em cada navegador, insira um nome exclusivo.

1. Agora, adicione um comentário e selecione **enviar**. Repita que em outros navegadores. Os comentários são exibidos em tempo real.

    > [!NOTE]
    > Este aplicativo de bate-papo simples não mantém o contexto de discussão no servidor. O hub transmite seus comentários para todos os usuários atuais. Usuários que participam de bate-papo mais tarde verá mensagens adicionadas desde o momento em que entrarem.

    Veja como o aplicativo de bate-papo é executado em três diferentes navegadores. Quando o Tom, Anand e Susan enviam mensagens, todos os navegadores são atualizados em tempo real:

    ![Todos os três navegadores exibem ao mesmo histórico de bate-papo](tutorial-getting-started-with-signalr/_static/image4.png)

1. Na **Gerenciador de soluções**, inspecione o **documentos de Script** nó para o aplicativo em execução. Há um arquivo de script denominado *hubs* que a biblioteca SignalR gera em tempo de execução. Este arquivo gerencia a comunicação entre o script jQuery e o código do lado do servidor.

    ![script de hubs gerado automaticamente no nó documentos de Script](tutorial-getting-started-with-signalr/_static/image5.png)

## <a name="examine-the-code"></a>Examinar o código

O aplicativo SignalRChat demonstra duas tarefas básicas de desenvolvimento de SignalR. Ele mostra como criar um hub. O servidor usa esse hub de que o objeto principal de coordenação. O hub usa a biblioteca jQuery SignalR para enviar e receber mensagens.

### <a name="signalr-hubs-in-the-chathubcs"></a>Hubs de SignalR no ChatHub.cs

No exemplo de código acima, o `ChatHub` classe deriva de `Microsoft.AspNet.SignalR.Hub` classe. Derivando a `Hub` classe é uma maneira útil para criar um aplicativo do SignalR. Você pode criar métodos públicos em sua classe de hub e, em seguida, usar esses métodos chamando-as de scripts em uma página da web.

No código de bate-papo, os clientes chamam o `ChatHub.Send` método para enviar uma nova mensagem. O hub, em seguida, envia a mensagem a todos os clientes chamando `Clients.All.broadcastMessage`.

O `Send` método demonstra vários conceitos de hub:

* Declare métodos públicos em um hub para que os clientes possam chamá-los.

* Use o `Microsoft.AspNet.SignalR.Hub.Clients` propriedade dinâmica para se comunicar com todos os clientes conectados a esse hub.

* Chamar uma função no cliente (como o `broadcastMessage` função) para atualizar os clientes.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery-in-the-indexhtml"></a>O SignalR e jQuery em index. HTML

O *index. HTML* página no exemplo de código mostra como usar a biblioteca jQuery SignalR para se comunicar com um hub SignalR. O código realiza várias tarefas importantes. Ele declara um proxy para o hub de referência, declara uma função que o servidor pode chamar para enviar conteúdo aos clientes, e ele inicia uma conexão para enviar mensagens ao hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> Em JavaScript a referência para a classe de servidor e seus membros deve ser camelCase. As referências de exemplo de código a C# *ChatHub* classe no JavaScript, enquanto `chatHub`.

Este bloco de código, você cria uma função de retorno de chamada no script.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

A classe hub no servidor chama essa função para enviar atualizações de conteúdo para cada cliente. As duas linhas que a codificação HTML o conteúdo antes de exibi-los são opcionais e mostrar uma boa maneira de evitar a injeção de script.

Esse código abre uma conexão com o hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

> [!NOTE]
> Essa abordagem garante que o código estabelece uma conexão antes de ser executado o manipulador de eventos.

O código começa a conexão e, em seguida, passa a ele uma função para manipular o evento de clique no **enviar** botão na página HTML.

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o SignalR, consulte os seguintes recursos:

* [Projeto de SignalR](http://signalr.net)

* [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)

* [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial você:

> [!div class="checklist"]
> * Configurar o projeto
> * Executar a amostra
> * Examinado o código

Avance para o próximo artigo para saber como usar o SignalR e ao MVC 5.
> [!div class="nextstepaction"]
> [O SignalR 2 e MVC 5](tutorial-getting-started-with-signalr-and-mvc.md)