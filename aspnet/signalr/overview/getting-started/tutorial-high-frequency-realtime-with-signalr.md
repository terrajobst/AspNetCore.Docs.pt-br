---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Tutorial: Em tempo real de alta frequência com SignalR 2 | Microsoft Docs'
author: pfletcher
description: Este tutorial mostra como criar um aplicativo web que usa o SignalR do ASP.NET para fornecer funcionalidade de mensagens de alta frequência. Alta frequência de mensagens em...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 04ce650509268ee63daafe24bc8dcc9725aea16b
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287700"
---
<a name="tutorial-high-frequency-realtime-with-signalr-2"></a>Tutorial: Em tempo real de alta frequência com SignalR 2
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Baixe o projeto concluído](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

> Este tutorial mostra como criar um aplicativo web que usa o ASP.NET SignalR 2 para fornecer a funcionalidade de mensagens de alta frequência. Alta frequência de mensagens nesse caso, significa que as atualizações que são enviadas a uma taxa fixa; no caso deste aplicativo, até 10 mensagens por segundo.
>
> O aplicativo que você criará neste tutorial exibe uma forma que os usuários poderão arrastar. A posição da forma em todos os outros navegadores conectados será atualizada de acordo com a posição da forma arrastada usando atualizações constantes.
>
> Os conceitos apresentados neste tutorial tem aplicativos em jogos em tempo real e outros aplicativos de simulação.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
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

Este tutorial demonstra como criar um aplicativo que compartilha o estado de um objeto com outros navegadores em tempo real. O aplicativo que criaremos é chamado MoveShape. A página de MoveShape exibirá um elemento HTML Div que o usuário pode arrastar; Quando o usuário arrasta o Div, sua nova posição será enviada para o servidor, que, em seguida, informa todos os outros clientes conectados para atualizar a posição da forma para corresponder.

![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

O aplicativo criado neste tutorial se baseia em uma demonstração por Damian Edwards. Um vídeo que contém esta demonstração pode ser visto [aqui](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).

Começar o tutorial que demonstra como enviar mensagens do SignalR de cada evento que é acionado como a forma é arrastada. Todos os clientes conectados, em seguida, atualizará a posição da versão local da forma cada vez que uma mensagem é recebida.

Enquanto o aplicativo funcione usando esse método, isso não é um modelo de programação recomendado, pois não haveria nenhum limite superior ao número de mensagens obtendo enviadas, portanto, os clientes e o servidor podem obter sobrecarregados com mensagens e seria prejudicar o desempenho . A animação exibida no cliente também seria não contíguos, como a forma seria movida instantaneamente por cada método, em vez de movimentação perfeitamente para cada novo local. As seções posteriores do tutorial demonstrará como criar uma função de temporizador que restringe a taxa máxima em que as mensagens são enviadas pelo cliente ou servidor e como movê-la sem problemas entre locais. A versão final do aplicativo criado neste tutorial pode ser baixada do [Galeria de códigos](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).

Este tutorial contém as seções a seguir:

- [Pré-requisitos](#prerequisites)
- [Criar o projeto e adicionar o pacote NuGet de JQuery.UI e SignalR](#createtheproject2013)
- [Criar o aplicativo básico](#baseapp)
- [Iniciando o hub quando o aplicativo é iniciado](#startup2013)
- [Adicionar o loop de cliente](#clientloop)
- [Adicionar o loop do servidor](#serverloop)
- [Adicionar animações suaves no cliente](#animation)
- [Etapas adicionais](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o Visual Studio 2013.

<a id="createtheproject2013"></a>

## <a name="create-the-project-and-add-the-signalr-and-jqueryui-nuget-package"></a>Criar o projeto e adicionar o pacote NuGet de JQuery.UI e SignalR

Nesta seção, vamos criar o projeto no Visual Studio 2013.

As etapas a seguir usam o Visual Studio 2013 para criar um aplicativo Web vazio do ASP.NET e adicionar as bibliotecas de SignalR e jQuery.UI:

1. No Visual Studio, crie um aplicativo Web ASP.NET.

    ![Criar web](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)
2. No **novo projeto ASP.NET** janela, deixe **vazia** selecionado e clique em **criar projeto**.

    ![Criar web vazio](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
3. Na **Gerenciador de soluções**, clique com botão direito no projeto, selecione **adicionar | Classe de Hub do SignalR (v2)**. Nomeie a classe **MoveShapeHub.cs** e adicioná-lo ao projeto. Esta etapa cria o **MoveShapeHub** de classe e o adiciona ao projeto de um conjunto de arquivos de script e referências de assembly que dão suporte ao SignalR.

    > [!NOTE]
    > Você também pode adicionar o SignalR para um projeto, clicando em **Ferramentas > Gerenciador de pacotes NuGet > Console do Gerenciador de pacotes** e executando um comando:

    `install-package Microsoft.AspNet.SignalR`.

    Se você usar o console para adicionar o SignalR, crie a classe de hub do SignalR como uma etapa separada depois de adicionar o SignalR.
4. Clique em **Ferramentas > Gerenciador de pacotes NuGet > Console do Gerenciador de pacotes**. Na janela do Gerenciador de pacote, execute o seguinte comando:

    `Install-Package jQuery.UI.Combined`

    Isso instala a biblioteca de interface do usuário do jQuery, que você usará para animar a forma.
5. Na **Gerenciador de soluções** expanda o nó de Scripts. Bibliotecas de script para o jQuery, jQueryUI e SignalR são visíveis no projeto.

    ![Referências da biblioteca de script](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>Criar o aplicativo básico

Nesta seção, vamos criar um aplicativo de navegador que envia o local da forma para o servidor durante cada evento de movimentação do mouse. O servidor, em seguida, transmite essas informações para todos os outros clientes conectados conforme ela é recebida. Expandiremos neste aplicativo nas próximas seções.

1. Se você ainda não criou a classe MoveShapeHub.cs, na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add**, **classe...** . Nomeie a classe **MoveShapeHub** e clique em **Add**.
2. Substitua o código no novo **MoveShapeHub** classe pelo código a seguir.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

    O `MoveShapeHub` classe acima é uma implementação de um hub SignalR. Como mostra a [Introdução ao SignalR](tutorial-getting-started-with-signalr.md) tutorial, o hub tem um método que os clientes chamarão diretamente. Nesse caso, o cliente envia um objeto que contém as novas coordenadas X e Y da forma para o servidor, que, em seguida, obtém difundida para todos os outros clientes conectados. O SignalR automaticamente serializará deste objeto usando JSON.

    O objeto que será enviado ao cliente (`ShapeModel`) contém membros para armazenar a posição da forma. A versão do objeto no servidor também contém um membro para controlar dados do cliente que estão sendo armazenados, para que um determinado cliente não será enviado a seus próprios dados. Esse membro usa o `JsonIgnore` atributo para impedir que ele está sendo serializado e enviado ao cliente.

<a id="startup2013"></a>
## <a name="starting-the-hub-when-the-application-starts"></a>Iniciando o hub quando o aplicativo é iniciado

1. Em seguida, vamos definir o mapeamento para o hub quando o aplicativo é iniciado. No SignalR 2, isso é feito pela adição de uma classe de inicialização OWIN, que chamará `MapSignalR` quando a classe de inicialização `Configuration` método é executado quando o OWIN é iniciado. A classe é adicionada à inicialização do OWIN processar usando a `OwinStartup` atributo do assembly.

    Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Classe de inicialização OWIN**. Nomeie a classe *inicialização* e clique em **Okey**.
2. Altere o conteúdo do Startup.cs para o seguinte:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<a id="client"></a>
## <a name="adding-the-client"></a>Adicionando o cliente

1. Em seguida, vamos adicionar o cliente. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Novo Item**. No **Adicionar Novo Item** caixa de diálogo, selecione **Página Html**. Nomeie a página **default. HTML** e clique em **Add**.
2. Na **Gerenciador de soluções**, a página que você acabou de criar com o botão direito e clique em **definir como página inicial**.
3. Substitua o código padrão na página HTML com o seguinte trecho de código.

    > [!NOTE]
    > Verifique se o script referencia abaixo correspondência os pacotes adicionados ao seu projeto na pasta Scripts.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html)]

    O código HTML e JavaScript acima cria um Div vermelho chamado forma, habilita o comportamento de arrastar da forma usando a biblioteca jQuery e usa a forma `drag` eventos para enviar a posição da forma para o servidor.
4. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>Adicionar o loop de cliente

Como enviar o local da forma em cada evento de movimentação do mouse, você criará uma quantidade desnecessários do tráfego de rede, as mensagens do cliente precisará ser limitadas. Vamos usar o javascript `setInterval` função para configurar um loop que envia novas informações de posição para o servidor a uma taxa fixa. Esse loop é uma representação muito básica de um "loop do jogo", uma função chamada repetidamente que conduz toda a funcionalidade de um jogo ou outra simulação.

1. Atualize o código de cliente na página HTML para corresponder ao seguinte trecho de código.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html)]

    A atualização acima adiciona o `updateServerModel` função, que é chamada em uma frequência fixa. Essa função envia os dados de posição para o servidor sempre que o `moved` sinalizador indica que há novos dados de posição para enviar.
2. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover. Uma vez que o número de mensagens que são enviados ao servidor será limitado, a animação não aparecerá tão simples como na seção anterior.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>Adicionar o loop do servidor

No aplicativo atual, as mensagens enviadas do servidor para o cliente sair sempre que elas são recebidas. Isso apresenta um problema semelhante, como foi visto no cliente; as mensagens podem ser enviadas com mais frequência que eles são necessários e a conexão foi inundada como resultado. Esta seção descreve como atualizar o servidor para implementar um temporizador que limita a taxa das mensagens de saída.

1. Substitua o conteúdo do `MoveShapeHub.cs` com o seguinte trecho de código.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    O código acima expande o cliente a ser adicionado a `Broadcaster` classe, que limita a saída de mensagens usando o `Timer` classe do .NET framework.

    Uma vez que o hub em si é transitório (ele é criado toda vez que ela é necessária), o `Broadcaster` será criado como um singleton. Inicialização lenta (introduzida no .NET 4) é usada para adiar a criação até que seja necessário, garantindo que a primeira instância de hub é criada completamente antes do temporizador é iniciado.

    A chamada para os clientes `UpdateShape` função, em seguida, é movida para fora do hub `UpdateModel` método, por que ele não é chamado imediatamente, sempre que as mensagens de entrada são recebidas. Em vez disso, as mensagens para os clientes serão enviadas em uma taxa de chamadas de 25 por segundo, gerenciado pelo `_broadcastLoop` temporizador de dentro de `Broadcaster` classe.

    Por fim, em vez de chamar o método de cliente do hub diretamente, o `Broadcaster` classe precisa obter uma referência para o hub de operação no momento (`_hubContext`) usando o `GlobalHost`.
2. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover. Não haverá uma diferença visível no navegador da seção anterior, mas o número de mensagens que são enviados ao cliente será limitado.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>Adicionar animações suaves no cliente

O aplicativo está quase concluído, mas poderíamos criar um mais aprimoramento, o movimento da forma no cliente quando ele é movida em resposta às mensagens de servidor. Em vez de definir a posição da forma para o novo local fornecido pelo servidor, vamos usar a biblioteca de JQuery UI `animate` função para mover a forma perfeita entre sua posição atual e novo.

1. Atualizar o cliente `updateShape` método procurar, como o código realçado abaixo:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

    O código acima move a forma do local antigo para o novo fornecido pelo servidor ao longo do intervalo de animação (nesse caso, 100 milissegundos). Qualquer em execução na forma de animação anterior é limpo antes de inicia a nova animação.
2. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover. A movimentação da forma em outra janela deve aparecer menos brusco conforme sua movimentação é interpolada ao longo do tempo em vez de ser definida uma vez por mensagem de entrada.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image8.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>Etapas adicionais

Neste tutorial, você aprendeu como programar um aplicativo de SignalR que envia mensagens de alta frequência entre clientes e servidores. Esse paradigma de comunicação é útil para o desenvolvimento de jogos online e outras simulações, tais como [ShootR jogo criado com o SignalR](https://shootr.azurewebsites.net/).

O aplicativo completo criado neste tutorial pode ser baixado em [Galeria de códigos](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).

Para saber mais sobre conceitos de desenvolvimento do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:

- [Projeto de SignalR](http://signalr.net)
- [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)
- [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)

Para obter instruções sobre como implantar um aplicativo de SignalR no Azure, consulte [usando o SignalR com aplicativos Web no serviço de aplicativo do Azure](../deployment/using-signalr-with-azure-web-sites.md). Para obter informações detalhadas sobre como implantar um projeto de web do Visual Studio para um Site do Windows Azure, consulte [criar um aplicativo web ASP.NET no serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).
