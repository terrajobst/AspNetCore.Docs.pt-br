---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Tutorial: Criar o aplicativo em tempo real de alta frequência com SignalR 2 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como criar um aplicativo web que usa o SignalR do ASP.NET para fornecer funcionalidade de mensagens de alta frequência.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 85503db0b41be6f87136627667d6dd71f0d4f609
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098584"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a>Tutorial: Criar o aplicativo em tempo real de alta frequência com SignalR 2

Este tutorial mostra como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer a funcionalidade de mensagens de alta frequência. Nesse caso, "mensagens de alta frequência" significa que o servidor envia atualizações a uma taxa fixa. Você envia mensagens de até 10 um segundo.

O aplicativo que você cria exibe uma forma que os usuários poderão arrastar. O servidor atualiza a posição da forma em todos os navegadores conectados para coincidir com a posição da forma arrastada usando atualizações constantes.

Os conceitos apresentados neste tutorial tem aplicativos em jogos em tempo real e outros aplicativos de simulação.

Neste tutorial, você:

> [!div class="checklist"]
> * Configurar o projeto
> * Criar o aplicativo básico
> * Mapear para o hub quando o aplicativo é iniciado
> * Adicionar o cliente
> * Executar o aplicativo
> * Adicionar o loop de cliente
> * Adicionar o loop do servidor
> * Adicionar animações suaves

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) com o **ASP.NET e desenvolvimento web** carga de trabalho.

## <a name="set-up-the-project"></a>Configurar o projeto

Nesta seção, você criará o projeto no Visual Studio 2017.

Esta seção mostra como usar o Visual Studio 2017 para criar um aplicativo de Web ASP.NET vazio e adicionar as bibliotecas de SignalR e jQuery.UI.

1. No Visual Studio, crie um aplicativo Web ASP.NET.

    ![Criar web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. No **novo aplicativo de Web do ASP.NET - MoveShapeDemo** janela, deixe **vazia** selecionado e selecione **Okey**.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.

1. Na **Adicionar Novo Item – MoveShapeDemo**, selecione **instalado** > **Visual C#**   >  **Web**  >  **SignalR** e, em seguida, selecione **classe de Hub do SignalR (v2)**.

1. Nomeie a classe *MoveShapeHub* e adicioná-lo ao projeto.

    Esta etapa cria o *MoveShapeHub.cs* arquivo de classe. Ao mesmo tempo, ele adiciona um conjunto de arquivos de script e referências de assembly que dão suporte ao SignalR ao projeto.

1. Selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **Package Manager Console**.

1. Na **Package Manager Console**, execute este comando:

    ```console
    Install-Package jQuery.UI.Combined
    ```

    O comando instala a biblioteca de interface do usuário do jQuery. Você pode usá-lo para animar a forma.

1. Na **Gerenciador de soluções**, expanda o nó de Scripts.

    ![Referências da biblioteca de script](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    Bibliotecas de script para o jQuery, jQueryUI e SignalR são visíveis no projeto.

## <a name="create-the-base-application"></a>Criar o aplicativo básico

Nesta seção, você criará um aplicativo de navegador. O aplicativo envia o local da forma para o servidor durante cada evento de movimentação do mouse. O servidor transmite essas informações para todos os outros clientes conectados em tempo real. Você aprenderá mais sobre este aplicativo nas próximas seções.

1. Abra o *MoveShapeHub.cs* arquivo.

1. Substitua o código na *MoveShapeHub.cs* arquivo com este código:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. Salve o arquivo.

O `MoveShapeHub` classe é uma implementação de um hub SignalR. Como mostra a [Introdução ao SignalR](tutorial-getting-started-with-signalr.md) tutorial, o hub tem um método que os clientes chamam diretamente. Nesse caso, o cliente envia coordena a um objeto com o novo X e Y da forma no servidor. Essas coordenadas obterem envia para todos os outros clientes conectados. O SignalR serializa automaticamente deste objeto usando JSON.

O aplicativo envia o `ShapeModel` objeto para o cliente. Ele tem membros para armazenar a posição da forma. A versão do objeto no servidor também tem um membro para controlar dados do cliente que estão sendo armazenados. Esse objeto impede que o servidor enviar dados de um cliente para si mesmo. Esse membro usa o `JsonIgnore` atributo para manter o aplicativo de serializar os dados e enviá-la de volta ao cliente.

## <a name="map-to-the-hub-when-app-starts"></a>Mapear para o hub quando o aplicativo é iniciado

Em seguida, configure o mapeamento para o hub quando o aplicativo é iniciado. No SignalR 2, adicionar uma classe de inicialização OWIN cria o mapeamento.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **Novo Item**.

1. Na **Adicionar Novo Item – MoveShapeDemo** selecionar **instalado** > **Visual C#**   >  **Web** e, em seguida, Selecione **classe de inicialização OWIN**.

1. Nomeie a classe *inicialização* e selecione **Okey**.

1. Substitua o código padrão a *Startup.cs* arquivo com este código:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

As chamadas de classe de inicialização OWIN `MapSignalR` quando o aplicativo executa o `Configuration` método. O aplicativo adiciona a classe para inicialização do OWIN processar usando a `OwinStartup` atributo do assembly.

## <a name="add-the-client"></a>Adicionar o cliente

Adicione página HTML para o cliente.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add** > **página HTML**.

1. Nomeie a página **padrão** e selecione **Okey**.

1. Na **Gerenciador de soluções**, clique com botão direito *default. HTML* e selecione **Set as Start Page**.

1. Substitua o código padrão a *default. HTML* arquivo com este código:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. Na **Gerenciador de soluções**, expanda **Scripts**.

    Bibliotecas de script para o jQuery e o SignalR são visíveis no projeto.

    > [!IMPORTANT]
    > O Gerenciador de pacotes instala uma versão mais recente dos scripts do SignalR.

1. Atualize as referências de script no bloco de código para corresponder às versões dos arquivos de script no projeto.

Esse código HTML e JavaScript cria um vermelho `div` chamado `shape`. Ele habilita o comportamento de arrastar da forma usando a biblioteca jQuery e usa o `drag` eventos para enviar a posição da forma para o servidor.

## <a name="run-the-app"></a>Executar o aplicativo

Você pode executar o aplicativo para se'e ele funciona. Quando você arrasta a forma em torno de uma janela do navegador, a forma se move em outros navegadores muito.

1. Na barra de ferramentas, ative **depuração de Script** e, em seguida, selecione o botão Reproduzir para executar o aplicativo no modo de depuração.

    ![Captura de tela do usuário ativar o modo de depuração e selecionando play.](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    Uma janela do navegador é aberta com o círculo vermelho no canto superior direito.

1. Copie a URL da página.

1. Abra outro navegador e cole a URL na barra de endereços.

1. Arraste a forma de uma das janelas do navegador. Segue a forma na janela do navegador.

Enquanto o aplicativo de funções usando esse método, não é um modelo de programação recomendado. Não há nenhum limite superior ao número de mensagens enviadas de Introdução. Como resultado, os clientes e servidor obterem sobrecarregados com mensagens e degrada o desempenho. Além disso, o aplicativo exibe uma animação não contíguo no cliente. Essa animação brusco acontece porque a forma se move instantaneamente por cada método. É melhor se a forma se move tranquilamente para cada novo local. Em seguida, você aprenderá a corrigir esses problemas.

## <a name="add-the-client-loop"></a>Adicionar o loop de cliente

Enviar o local da forma em cada evento de movimentação do mouse cria uma quantidade desnecessária de tráfego de rede. O aplicativo precisa acelerar as mensagens do cliente.

Usar o javascript `setInterval` função para configurar um loop que envia novas informações de posição para o servidor a uma taxa fixa. Esse loop é uma representação básica de um "loop do jogo". É uma função chamada repetidamente que conduz toda a funcionalidade de um jogo.

1. Substitua o código de cliente na *default. HTML* arquivo com este código:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > Você precisará substituir as referências de script novamente. Eles devem coincidir com as versões dos scripts no projeto.

    Esse novo código adiciona o `updateServerModel` função. Ele é chamado em uma frequência fixa. A função envia os dados de posição para o servidor sempre que o `moved` sinalizador indica que há novos dados de posição para enviar.

1. Selecione o botão play para iniciar o aplicativo

1. Copie a URL da página.

1. Abra outro navegador e cole a URL na barra de endereços.

1. Arraste a forma de uma das janelas do navegador. Segue a forma na janela do navegador.

Uma vez que o aplicativo limita o número de mensagens que são enviados ao servidor, a animação não aparecerá como smooth fez no início.

## <a name="add-the-server-loop"></a>Adicionar o loop do servidor

No aplicativo atual, as mensagens enviadas do servidor para o cliente sair sempre que são recebidas. Esse tráfego de rede apresenta um problema semelhante, como vemos no cliente.

O aplicativo pode enviar mensagens com mais frequência que eles são necessários. A conexão pode inundada como resultado. Esta seção descreve como atualizar o servidor para adicionar um temporizador que limita a taxa das mensagens de saída.

1. Substitua o conteúdo do `MoveShapeHub.cs` com este código:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. Selecione o botão play para iniciar o aplicativo.

1. Copie a URL da página.

1. Abra outro navegador e cole a URL na barra de endereços.

1. Arraste a forma de uma das janelas do navegador.

Esse código expande o cliente para adicionar o `Broadcaster` classe. A nova classe limita as mensagens de saída usando o `Timer` classe do .NET framework.

É bom saber que o hub em si é transitório. Ele é criado sempre que for necessário. Portanto, o aplicativo cria o `Broadcaster` como um singleton. Ele usa a inicialização lenta para adiar o `Broadcaster`da criação até que ele seja necessário. Que garante que o aplicativo cria a primeira instância de hub completamente antes de iniciar o temporizador.

A chamada para os clientes `UpdateShape` função, em seguida, é movida para fora do hub `UpdateModel` método. Ele não é chamado imediatamente, sempre que o aplicativo recebe mensagens de entrada. Em vez disso, o aplicativo envia mensagens para os clientes a uma taxa de chamadas de 25 por segundo. O processo é gerenciado pelo `_broadcastLoop` temporizador de dentro de `Broadcaster` classe.

Por fim, em vez de chamar o método de cliente do hub diretamente, o `Broadcaster` classe precisa obter uma referência à operação atualmente `_hubContext` hub. Ele obtém a referência com o `GlobalHost`.

## <a name="add-smooth-animation"></a>Adicionar animações suaves

O aplicativo está quase terminando, mas poderíamos criar uma melhoria de mais. O aplicativo move a forma no cliente em resposta às mensagens de servidor. Em vez de definir a posição da forma para o novo local fornecido pelo servidor, use a biblioteca de JQuery UI `animate` função. Ele pode mover a forma sem problemas entre sua posição atual e novo.

1. Atualizar o cliente `updateShape` método na *default. HTML* arquivo para se parecer com o código realçado:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. Selecione o botão play para iniciar o aplicativo.

1. Copie a URL da página.

1. Abra outro navegador e cole a URL na barra de endereços.

1. Arraste a forma de uma das janelas do navegador.

A movimentação da forma em outra janela aparece menos brusco. O aplicativo interpola seu movimento ao longo do tempo em vez de ser definida uma vez por mensagem de entrada.

Esse código move a forma do local antigo para o novo. O servidor fornece a posição da forma ao longo do intervalo de animação. Nesse caso, que é 100 milissegundos. O aplicativo limpa qualquer animação anterior em execução na forma antes de inicia a nova animação.

## <a name="additional-resources"></a>Recursos adicionais

O paradigma de comunicação que você acabou de aprender sobre é útil para desenvolvimento de jogos online e outras simulações, como [ShootR jogo criado com o SignalR](https://shootr.azurewebsites.net/).

Para obter mais informações sobre o SignalR, consulte os seguintes recursos:

* [Projeto de SignalR](http://signalr.net)

* [GitHub do SignalR e exemplos](https://github.com/SignalR/SignalR)

* [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Configurar o projeto
> * Criado o aplicativo básico
> * Mapeado para o hub quando o aplicativo é iniciado
> * Adicionou o cliente
> * Executei o aplicativo
> * Adicionado o loop de cliente
> * Adicionado o loop do servidor
> * Adição de animação suave

Avance para o próximo artigo para saber como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer funcionalidade de difusão de servidor.
> [!div class="nextstepaction"]
> [SignalR 2 e a transmissão de servidor](tutorial-server-broadcast-with-signalr.md)