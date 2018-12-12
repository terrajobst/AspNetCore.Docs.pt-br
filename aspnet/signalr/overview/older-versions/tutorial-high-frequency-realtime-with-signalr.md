---
uid: signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
title: Em tempo real de alta frequência com SignalR 1.x | Microsoft Docs
author: pfletcher
description: Este tutorial mostra como criar um aplicativo web que usa o SignalR do ASP.NET para fornecer funcionalidade de mensagens de alta frequência. Alta frequência de mensagens em...
ms.author: riande
ms.date: 04/16/2013
ms.assetid: ad2a5da5-2e79-40ea-bc84-028d327f5982
msc.legacyurl: /signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a679a7c66a94fa440a1ead64225eb86f7de90c9e
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287950"
---
<a name="high-frequency-realtime-with-signalr-1x"></a>Em tempo real de alta frequência com SignalR 1.x
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tutorial mostra como criar um aplicativo web que usa o SignalR do ASP.NET para fornecer funcionalidade de mensagens de alta frequência. Alta frequência de mensagens nesse caso, significa que as atualizações que são enviadas a uma taxa fixa; no caso deste aplicativo, até 10 mensagens por segundo.
> 
> O aplicativo que você criará neste tutorial exibe uma forma que os usuários poderão arrastar. A posição da forma em todos os outros navegadores conectados será atualizada de acordo com a posição da forma arrastada usando atualizações constantes.
> 
> Os conceitos apresentados neste tutorial tem aplicativos em jogos em tempo real e outros aplicativos de simulação.
> 
> O tutorial, os comentários são boas-vindos. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com).


## <a name="overview"></a>Visão geral

Este tutorial demonstra como criar um aplicativo que compartilha o estado de um objeto com outros navegadores em tempo real. O aplicativo que criaremos é chamado MoveShape. A página de MoveShape exibirá um elemento HTML Div que o usuário pode arrastar; Quando o usuário arrasta o Div, sua nova posição será enviada para o servidor, que, em seguida, informa todos os outros clientes conectados para atualizar a posição da forma para corresponder.

![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

O aplicativo criado neste tutorial se baseia em uma demonstração por Damian Edwards. Um vídeo que contém esta demonstração pode ser visto [aqui](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).

Começar o tutorial que demonstra como enviar mensagens do SignalR de cada evento que é acionado como a forma é arrastada. Todos os clientes conectados, em seguida, atualizará a posição da versão local da forma cada vez que uma mensagem é recebida.

Enquanto o aplicativo funcione usando esse método, isso não é um modelo de programação recomendado, pois não haveria nenhum limite superior ao número de mensagens obtendo enviadas, portanto, os clientes e o servidor podem obter sobrecarregados com mensagens e seria prejudicar o desempenho . A animação exibida no cliente também seria não contíguos, como a forma seria movida instantaneamente por cada método, em vez de movimentação perfeitamente para cada novo local. As seções posteriores do tutorial demonstrará como criar uma função de temporizador que restringe a taxa máxima em que as mensagens são enviadas pelo cliente ou servidor e como movê-la sem problemas entre locais. A versão final do aplicativo criado neste tutorial pode ser baixada do [Galeria de códigos](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

Este tutorial contém as seções a seguir:

- [Pré-requisitos](#prerequisites)
- [Criar o projeto](#createtheproject)
- [Adicione os pacotes do NuGet de JQuery.UI e SignalR do ASP.NET](#nugetpackages)
- [Criar o aplicativo básico](#baseapp)
- [Adicionar o loop de cliente](#clientloop)
- [Adicionar o loop do servidor](#serverloop)
- [Adicionar animações suaves no cliente](#animation)
- [Etapas adicionais](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer o Visual Studio 2012 ou Visual Studio 2010. Se for usado o Visual Studio 2010, o projeto usará o .NET Framework 4, em vez do .NET Framework 4.5.

Se você estiver usando o Visual Studio 2012, é recomendável que você instale o [atualização do ASP.NET e Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650). Esta atualização contém novos recursos como os aprimoramentos à funcionalidade de publicação, novo e novos modelos.

Se você tiver o Visual Studio 2010, verifique se [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) está instalado.

<a id="createtheproject"></a>

## <a name="create-the-project"></a>Criar o projeto

Nesta seção, vamos criar o projeto no Visual Studio.

1. Dos **arquivo** menu, clique em **novo projeto**.
2. No **novo projeto** diálogo caixa, expanda **c#** sob **modelos** e selecione **Web**.
3. Selecione o **aplicativo Web vazio ASP.NET** modelo, o nome do projeto *MoveShapeDemo*e clique em **Okey**.

    ![Criar o novo projeto](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-and-jqueryui-nuget-packages"></a>Adicionar o SignalR e pacotes do JQuery.UI NuGet

Você pode adicionar funcionalidade de SignalR para um projeto por meio da instalação de um pacote do NuGet. Este tutorial também usará o pacote JQuery.UI para permitir que a forma sejam arrastadas e animada.

1. Clique em **ferramentas | Gerenciador de pacotes NuGet | Package Manager Console**.
2. Digite o seguinte comando no Gerenciador de pacotes.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.ps1)]

    O pacote do SignalR instala um número de outros pacotes do NuGet como dependências. Quando a instalação for concluída você tem todos os componentes de servidor e cliente necessários para usar o SignalR em um aplicativo ASP.NET.
3. Digite o seguinte comando no console do Gerenciador de pacotes para instalar os pacotes do JQuery e JQuery.UI.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.ps1)]

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>Criar o aplicativo básico

Nesta seção, vamos criar um aplicativo de navegador que envia o local da forma para o servidor durante cada evento de movimentação do mouse. O servidor, em seguida, transmite essas informações para todos os outros clientes conectados conforme ela é recebida. Expandiremos neste aplicativo nas próximas seções.

1. Na **Gerenciador de soluções**, clique com botão direito no projeto e selecione **Add**, **classe...** . Nomeie a classe **MoveShapeHub** e clique em **Add**.
2. Substitua o código no novo **MoveShapeHub** classe pelo código a seguir.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.cs)]

    O `MoveShapeHub` classe acima é uma implementação de um hub SignalR. Como mostra a [Introdução ao SignalR](index.md) tutorial, o hub tem um método que os clientes chamarão diretamente. Nesse caso, o cliente envia um objeto que contém as novas coordenadas X e Y da forma para o servidor, que, em seguida, obtém difundida para todos os outros clientes conectados. O SignalR automaticamente serializará deste objeto usando JSON.

    O objeto que será enviado ao cliente (`ShapeModel`) contém membros para armazenar a posição da forma. A versão do objeto no servidor também contém um membro para controlar dados do cliente que estão sendo armazenados, para que um determinado cliente não será enviado a seus próprios dados. Esse membro usa o `JsonIgnore` atributo para impedir que ele está sendo serializado e enviado ao cliente.
3. Em seguida, vamos definir o hub quando o aplicativo é iniciado. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Classe de aplicativo global**. Aceite o nome padrão de *Global* e clique em **Okey**.

    ![Adicionar classe de aplicativo Global](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
4. Adicione o seguinte `using` instrução após fornecido **usando** instruções na classe Global.asax.cs.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.cs)]
5. Adicione a seguinte linha de código no `Application_Start` método da classe Global para registrar a rota padrão para o SignalR.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    O arquivo global. asax deve ser semelhante ao seguinte:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.cs)]
6. Em seguida, vamos adicionar o cliente. Na **Gerenciador de soluções**, clique com botão direito no projeto e clique em **adicionar | Novo Item**. No **Adicionar Novo Item** caixa de diálogo, selecione **Página Html**. Dê um nome apropriado de página (como **default. HTML**) e clique em **Add**.
7. Na **Gerenciador de soluções**, a página que você acabou de criar com o botão direito e clique em **definir como página inicial**.
8. Substitua o código padrão na página HTML com o seguinte trecho de código.

    > [!NOTE]
    > Verifique se o script referencia abaixo correspondência os pacotes adicionados ao seu projeto na pasta Scripts. No Visual Studio 2010, a versão do JQuery e SignalR adicionado ao projeto pode não corresponder os números de versão a seguir.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample7.html)]

    O código HTML e JavaScript acima cria um Div vermelho chamado forma, habilita o comportamento de arrastar da forma usando a biblioteca jQuery e usa a forma `drag` eventos para enviar a posição da forma para o servidor.
9. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>Adicionar o loop de cliente

Como enviar o local da forma em cada evento de movimentação do mouse, você criará uma quantidade desnecessários do tráfego de rede, as mensagens do cliente precisará ser limitadas. Vamos usar o javascript `setInterval` função para configurar um loop que envia novas informações de posição para o servidor a uma taxa fixa. Esse loop é uma representação muito básica de um "loop do jogo", uma função chamada repetidamente que conduz toda a funcionalidade de um jogo ou outra simulação.

1. Atualize o código de cliente na página HTML para corresponder ao seguinte trecho de código.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample8.html)]

    A atualização acima adiciona o `updateServerModel` função, que é chamada em uma frequência fixa. Essa função envia os dados de posição para o servidor sempre que o `moved` sinalizador indica que há novos dados de posição para enviar.
2. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover. Uma vez que o número de mensagens que são enviados ao servidor será limitado, a animação não aparecerá tão simples como na seção anterior.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>Adicionar o loop do servidor

No aplicativo atual, as mensagens enviadas do servidor para o cliente sair sempre que elas são recebidas. Isso apresenta um problema semelhante, como foi visto no cliente; as mensagens podem ser enviadas com mais frequência que eles são necessários e a conexão foi inundada como resultado. Esta seção descreve como atualizar o servidor para implementar um temporizador que limita a taxa das mensagens de saída.

1. Substitua o conteúdo do `MoveShapeHub.cs` com o seguinte trecho de código.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample9.cs)]

    O código acima expande o cliente a ser adicionado a `Broadcaster` classe, que limita a saída de mensagens usando o `Timer` classe do .NET framework.

    Uma vez que o hub em si é transitório (ele é criado toda vez que ela é necessária), o `Broadcaster` será criado como um singleton. Inicialização lenta (introduzida no .NET 4) é usada para adiar a criação até que seja necessário, garantindo que a primeira instância de hub é criada completamente antes do temporizador é iniciado.

    A chamada para os clientes `UpdateShape` função, em seguida, é movida para fora do hub `UpdateModel` método, por que ele não é chamado imediatamente, sempre que as mensagens de entrada são recebidas. Em vez disso, as mensagens para os clientes serão enviadas em uma taxa de chamadas de 25 por segundo, gerenciado pelo `_broadcastLoop` temporizador de dentro de `Broadcaster` classe.

    Por fim, em vez de chamar o método de cliente do hub diretamente, o `Broadcaster` classe precisa obter uma referência para o hub de operação no momento (`_hubContext`) usando o `GlobalHost`.
2. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover. Não haverá uma diferença visível no navegador da seção anterior, mas o número de mensagens que são enviados ao cliente será limitado.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>Adicionar animações suaves no cliente

O aplicativo está quase concluído, mas poderíamos criar um mais aprimoramento, o movimento da forma no cliente quando ele é movida em resposta às mensagens de servidor. Em vez de definir a posição da forma para o novo local fornecido pelo servidor, vamos usar a biblioteca de JQuery UI `animate` função para mover a forma perfeita entre sua posição atual e novo.

1. Atualizar o cliente `updateShape` método procurar, como o código realçado abaixo:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample10.html?highlight=35-42)]

    O código acima move a forma do local antigo para o novo fornecido pelo servidor ao longo do intervalo de animação (nesse caso, 100 milissegundos). Qualquer em execução na forma de animação anterior é limpo antes de inicia a nova animação.
2. Inicie o aplicativo pressionando F5. Copie a URL da página e, em seguida, cole-o em uma segunda janela do navegador. Arraste a forma de uma das janelas de navegador; a forma na janela do navegador deve mover. A movimentação da forma em outra janela deve aparecer menos brusco conforme sua movimentação é interpolada ao longo do tempo em vez de ser definida uma vez por mensagem de entrada.

    ![A janela do aplicativo](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>Etapas adicionais

Neste tutorial, você aprendeu como programar um aplicativo de SignalR que envia mensagens de alta frequência entre clientes e servidores. Esse paradigma de comunicação é útil para o desenvolvimento de jogos online e outras simulações, tais como [ShootR jogo criado com o SignalR](http://shootr.signalr.net).

O aplicativo completo criado neste tutorial pode ser baixado em [Galeria de códigos](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

Para saber mais sobre conceitos de desenvolvimento do SignalR, visite os seguintes sites para o código-fonte SignalR e recursos:

- [Projeto de SignalR](http://signalr.net)
- [Github do SignalR e exemplos](https://github.com/SignalR/SignalR)
- [Wiki do SignalR](https://github.com/SignalR/SignalR/wiki)
